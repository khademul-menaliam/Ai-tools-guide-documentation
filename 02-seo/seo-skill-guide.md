# SEO Agent Skill Operating & Implementation Guide

This guide provides exhaustive instructions for using, extending, and maintaining the portable **SEO Agent Skill** located in `02-seo/.agent/skills/seo/`.

---

## 1. Skill Architecture & File Hierarchy

```text
02-seo/.agent/skills/seo/
├── SKILL.md                          # Main skill prompt & orchestrator
└── references/                       # On-demand knowledge files
    ├── core-seo-concepts.md          # Universal metadata, OG, JSON-LD, headings, classification
    ├── nextjs-react.md               # Next.js App/Pages Router & React SPAs
    ├── vue-nuxt.md                   # Vue 3 & Nuxt 3 (useSeoMeta / Unhead)
    ├── angular.md                    # Angular Title/Meta services & SSR
    ├── laravel.md                    # Laravel Blade, Inertia.js & Spatie SEO
    └── gsc-post-deploy.md            # GSC verification & post-launch tasks
```

---

## 2. Installing the Skill in a Target Project

To use this skill in any web development project:

1. Copy the `.agent` folder into the target repository root:
   ```bash
   cp -r /path/to/Ai-tools-guide/02-seo/.agent /path/to/my-project/
   ```
2. Verify the path exists: `/path/to/my-project/.agent/skills/seo/SKILL.md`.
3. Invoke your AI coding assistant (Cursor, Claude Code, Antigravity, etc.) with prompts such as:
   - *"Run an initial SEO audit on this project using the SEO skill."*
   - *"Audit new or modified pages for SEO compliance."*
   - *"Generate the deployment SEO guide for Search Console."*

---

## 3. Project-Specific State (`system-docs/`)

All project state created during audits is saved inside `system-docs/` in the target project root.

### 3.1 `system-docs/seo-config.json` Schema
Defines the detected environment and site parameters:

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "SeoConfig",
  "type": "object",
  "properties": {
    "schemaVersion": { "type": "string", "enum": ["1.0"] },
    "project": {
      "type": "object",
      "properties": {
        "name": { "type": "string" },
        "domain": { "type": "string" },
        "framework": { "type": "string", "enum": ["nextjs", "vue-nuxt", "angular", "laravel", "other"] },
        "router": { "type": "string" },
        "language": { "type": "string", "enum": ["typescript", "javascript", "php"] },
        "rendering": { "type": "string", "enum": ["SSR", "SSG", "CSR", "Hybrid", "Unknown"] },
        "renderingDetectedAutomatically": { "type": "boolean" },
        "detectedAutomatically": { "type": "boolean" },
        "userOverridden": { "type": "boolean" }
      },
      "required": ["name", "domain", "framework", "language", "rendering"]
    },
    "settings": {
      "type": "object",
      "properties": {
        "defaultLocale": { "type": "string" },
        "twitterHandle": { "type": "string" },
        "sitemapPath": { "type": "string" },
        "robotsPath": { "type": "string" }
      }
    },
    "updatedAt": { "type": "string", "format": "date-time" }
  },
  "required": ["schemaVersion", "project", "updatedAt"]
}
```

### 3.2 `system-docs/seo-tracker.json` Schema
Maintains incremental audit history per route, route classification metadata, content hashes, global layout hash, and status:

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "SeoTracker",
  "type": "object",
  "properties": {
    "schemaVersion": { "type": "string", "enum": ["1.0"] },
    "lastAuditTimestamp": { "type": "string", "format": "date-time" },
    "globalStatus": {
      "type": "object",
      "properties": {
        "sitemap": { "type": "string", "enum": ["complete", "needs_attention", "missing"] },
        "robots": { "type": "string", "enum": ["complete", "needs_attention", "missing"] },
        "gscVerified": { "type": "boolean" },
        "cwvChecked": { "type": "boolean" },
        "globalSeoHash": { "type": "string", "pattern": "^sha256:[a-f0-9]{64}$" }
      }
    },
    "routes": {
      "type": "object",
      "additionalProperties": {
        "type": "object",
        "properties": {
          "routeType": { "type": "string", "enum": ["page", "api", "admin", "redirect", "error", "asset", "unknown"] },
          "responseType": { "type": "string", "enum": ["html", "json", "redirect", "asset", "unknown"] },
          "isPublic": { "type": "boolean" },
          "isRedirect": { "type": "boolean" },
          "isSeoPageCandidate": { "type": "boolean" },
          "status": { "type": "string", "enum": ["complete", "needs_attention", "pending", "excluded"] },
          "lastAudited": { "type": "string", "format": "date-time" },
          "contentHash": { "type": "string", "pattern": "^sha256:[a-f0-9]{64}$" },
          "issuesResolved": { "type": "array", "items": { "type": "string" } },
          "pendingIssues": { "type": "array", "items": { "type": "string" } }
        },
        "required": ["routeType", "responseType", "isPublic", "isSeoPageCandidate", "status", "lastAudited", "contentHash", "issuesResolved", "pendingIssues"]
      }
    },
    "summary": {
      "type": "object",
      "properties": {
        "totalRoutes": { "type": "integer" },
        "seoPageCandidates": { "type": "integer" },
        "completedRoutes": { "type": "integer" },
        "pendingRoutes": { "type": "integer" },
        "excludedRoutes": { "type": "integer" }
      }
    }
  },
  "required": ["schemaVersion", "lastAuditTimestamp", "globalStatus", "routes", "summary"]
}
```

---

## 4. Operational Workflows

### 4.1 Initial Baseline Audit (Workflow 1)
When `system-docs/seo-config.json` is missing:
1. Agent inspects project dependencies (`package.json`, `composer.json`) and directories.
2. Stack, language, and rendering strategy (`SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown`) are determined conservatively.
3. If domain or site name are not found in config/env, agent asks user for target details.
4. `system-docs/seo-config.json` is created with `"schemaVersion": "1.0"`. Preserve existing user overrides if present.
5. Agent scans codebase to list all routes.
6. **Formal Route Classification**: Classify each route into `page`, `api`, `admin`, `redirect`, `error`, `asset`, or `unknown`. Determine `isSeoPageCandidate`.
7. **Build Verified SEO-Page Inventory**: Filter routes where `isSeoPageCandidate: true`.
8. **Calculate Hashes**: Compute deterministic `sha256` content hash for each route and compute `globalSeoHash` for shared SEO layout files.
9. **Audit Global SEO**: Check sitemap configuration, `robots.txt`, root layout/head fallbacks, and global JSON-LD (`Organization`/`WebSite`).
10. **Audit Page SEO**: Inspect **ONLY** verified `isSeoPageCandidate: true` routes for title, description, canonical, OG image, JSON-LD, headings, images, and links.
11. **Apply Safe Automated Fixes**: Update missing metadata, standard canonical tags, or correct structural formatting using framework conventions.
12. **Generate HTML Sitemap**: Include **ONLY** verified `isSeoPageCandidate: true` routes in `sitemap.xml`. Exclude API endpoints, redirects, admin routes, and static assets.
13. **Log Unresolved Items**: Mark items requiring business decisions as pending.
14. **Initialize Tracker**: Write findings to `system-docs/seo-tracker.json` with `"schemaVersion": "1.0"` including classification properties for all routes.
15. **Validate Route Inventory Consistency**: Ensure `verified SEO-page inventory == tracker SEO-page routes == sitemap candidates`. If any mismatch exists, halt execution and report an error immediately.
16. **Report Status**: Present a structured summary of completed fixes, excluded API/admin routes, and pending items to the user.

### 4.2 Incremental Subsequent Audit (Workflow 2)
When `system-docs/seo-config.json` and `seo-tracker.json` ALREADY exist:
1. Agent loads `seo-config.json` and `seo-tracker.json` (verifying `schemaVersion`).
2. Agent scans current project routes, re-classifies each route, calculates current `contentHash`, and computes `globalSeoHash`.
3. Agent checks for legacy tracker entries missing `contentHash`/classification or shared layout file modifications (`currentGlobalSeoHash != trackedGlobalSeoHash`).
4. Routes are classified into 4 distinct categories (`NEW`, `MODIFIED`, `UNCHANGED`, `DELETED`).
5. Confirmed `DELETED` routes are pruned from the active `routes` object in `seo-tracker.json`.
6. Only matching framework reference is loaded (e.g., `references/vue-nuxt.md`).
7. Agent audits all **`NEW`** and **`MODIFIED`** routes where `isSeoPageCandidate: true`. Non-SEO candidates (`api`, `admin`, `redirect`, `asset`) are skipped.
8. Fixes are applied and `seo-tracker.json` is updated with current timestamps, backfilled content hashes, and updated `globalSeoHash`.
9. HTML sitemap is regenerated with verified `isSeoPageCandidate: true` routes.
10. **Validate Route Inventory Consistency**: Confirm `verified SEO-page inventory == tracker SEO-page routes == sitemap candidates`. If any mismatch exists, halt execution and report an error immediately.
11. Present incremental progress report.

---

## 5. Adding Future Framework Support

To add support for new frameworks (e.g., SvelteKit, Remix, Astro, Ruby on Rails):

1. **Do NOT rewrite `SKILL.md`**.
2. Create a new reference file under `02-seo/.agent/skills/seo/references/` (e.g., `sveltekit.md`).
3. Add framework detection signal to the detection table in `SKILL.md` pointing to `references/sveltekit.md`.
4. Document framework-specific route classification rules (pages vs API server handlers) and metadata APIs in the new reference file.
