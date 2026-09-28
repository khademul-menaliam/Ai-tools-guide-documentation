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
        "sitemap": { "type": "string", "enum": ["complete", "needs_attention", "missing", "pending_domain"] },
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
          "routeType": { "type": "string", "enum": ["page", "transactional", "auth", "utility", "api", "admin", "redirect", "error", "asset", "unknown"] },
          "responseType": { "type": "string", "enum": ["html", "json", "redirect", "asset", "unknown"] },
          "isPublic": { "type": "boolean" },
          "isRedirect": { "type": "boolean" },
          "isSeoPageCandidate": { "type": "boolean" },
          "status": { "type": "string", "enum": ["complete", "needs_attention", "pending", "excluded"] },
          "metadataSource": {
            "type": "object",
            "properties": {
              "title": { "type": "string", "enum": ["page", "inherited", "missing"] },
              "description": { "type": "string", "enum": ["page", "inherited", "missing"] }
            }
          },
          "sitemapExpansionStatus": { "type": "string", "enum": ["complete", "pending", "unresolved", "not-applicable"] },
          "cwvImplementation": { "type": "string", "enum": ["checked", "unchecked"] },
          "cwvMeasurement": { "type": "string", "enum": ["measured", "not_measured"] },
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
3. **Ask Missing Essentials & Distinguish Domain vs API**: If production website domain or site name are not found in config/env, agent prompts user. Distinguishes website domain from backend API base URLs (`NEXT_PUBLIC_BASE_URL`, `http://backend.test`); never promotes backend API URLs to canonical website domains. If domain is unverified, sets `domain: "UNRESOLVED"`.
4. `system-docs/seo-config.json` is created with `"schemaVersion": "1.0"`. Preserve existing user overrides if present.
5. Agent scans codebase to list all routes.
6. **Multi-Stage Route Classification**: Execute classification pipeline:
   - Determine `responseType` (`html`, `json`, `redirect`, `asset`, `unknown`).
   - Determine `isPublic` from auth guards and session middleware (independent of robots.txt).
   - Classify `routeType` (`page`, `transactional`, `auth`, `utility`, `api`, `admin`, `redirect`, `error`, `asset`, `unknown`) based on implementation evidence (forms, mutations, auth flows, checkout logic, post-action states).
   - Assign `isSeoPageCandidate: true` ONLY for public `page` routes. Non-SEO HTML routes (`transactional`, `auth`, `utility`) receive `isSeoPageCandidate: false`.
7. **Build Verified SEO-Page Inventory**: Filter routes where `isSeoPageCandidate: true` (excludes non-SEO HTML routes, API endpoints, admin, redirects, error handlers, and static assets).
8. **Calculate Hashes**: Compute deterministic `sha256` content hash for each route and compute `globalSeoHash` for shared SEO layout files.
9. **Audit Global SEO**: Check sitemap configuration, `robots.txt`, root layout/head fallbacks, and global JSON-LD (`Organization`/`WebSite`).
10. **Audit Page SEO & Classify Findings**: Inspect **ONLY** verified `isSeoPageCandidate: true` routes in live code and classify findings:
    - `<title>` and `<meta name="description">`: evaluate `resolveMetadataField(route, "title")` and `resolveMetadataField(route, "description")` independently across the hierarchy (`page -> nearest applicable nested layout -> parent layout(s) -> root layout -> missing`). Inspect actual AST/exported metadata structure and `generateMetadata()` return fields (never infer `inherited` or `page` from unverified presence/absence or naive substring matches). Report concrete evidence citing exact declarations.
    - Next.js 15+ App Router: verify `generateMetadata({ params })` awaits `params` (`const { slug } = await params;`) before property access (`NEW ISSUE` if unawaited, `ALREADY FIXED` if awaited)
    - Canonical tags, OG tags, JSON-LD, heading hierarchy, images, and links
    - CWV static implementation hygiene (`cwvImplementation: "checked"`, `cwvMeasurement: "not_measured"`)
11. **Apply Safe Automated Fixes & Track File Modifications**:
    - Modify files ONLY for actionable `NEW ISSUE` findings. Validate fixes. On pass, record `Status: NEW ISSUE → FIXED` and add to `Files Modified`.
    - If code is already compliant, record `Status: ALREADY FIXED → NO CHANGE`, `Files Modified: 0`, and add to `Files Unchanged`. NEVER report "FOUND & FIXED" or only "FIXED" when no file changes occurred.
    - If blocked on missing external data, record `Status: UNRESOLVED → USER INPUT REQUIRED`.
12. **Generate HTML Sitemap (or Defer if Domain UNRESOLVED)**:
    - If `domain == "UNRESOLVED"`, DO NOT write a physical production sitemap containing absolute URLs with placeholder/fake domains (`example.com`, `localhost`). Record `globalStatus.sitemap: "pending_domain"`.
    - If `domain` is resolved, include **ONLY** verified `isSeoPageCandidate: true` routes in `sitemap.xml`. Exclude non-SEO HTML routes (`transactional`, `auth`, `utility`), API endpoints, redirects, admin routes, and static assets. Record `globalStatus.sitemap: "complete"`.
13. **Log Unresolved Items**: Mark items requiring business decisions as pending.
14. **Initialize Tracker**: Write findings to `system-docs/seo-tracker.json` with `"schemaVersion": "1.0"` including classification properties, `metadataSource`, and CWV status for all routes.
15. **Validate Route Inventory Consistency & Category Count Reconciliation**: Ensure `verified SEO-page inventory == tracker SEO-page routes == sitemap candidates`. Dynamically compute category counts from `seo-tracker.json` routes and confirm strict count reconciliation: `sum(excluded category counts) == total excluded routes` and `seoPageCandidates + total excluded routes == total discovered routes` (e.g., 11 SEO + 8 API + 2 Auth/Admin + 1 Storage = 22 total). If any mismatch exists, halt execution or correct the calculation so all counts reconcile before proceeding.
16. **Generate / Update Deployment Guide**: Automatically execute Workflow 3 to generate or update `system-docs/DEPLOYMENT-SEO-GUIDE.md` based on latest tracker state and verified configuration (even if the project is already SEO-compliant with 0 fixes needed). The user does NOT need to explicitly request this guide.
17. **Report Status & Conclude with Next Steps**: Present a structured summary with explicit breakdown of total discovered routes, SEO page candidates, excluded route categories (REST API, Auth/Admin, Storage/Assets, Transactional, Utility, Redirects, Errors, Unknown) with dynamically calculated counts reconciled with total routes (`seoPageCandidates + sum(excluded category counts) == total discovered routes`), finding lifecycle breakdown (`NEW ISSUE → FIXED`, `ALREADY FIXED → NO CHANGE`, `UNRESOLVED → USER INPUT REQUIRED`, `NO ISSUE`), `Files Inspected` vs `Files Modified` vs `Files Unchanged`, and conclude with the standardized `SEO AUDIT COMPLETE` Next Steps block (status counts, detailed non-zero lists, and dynamic next step derived from actual audit state).

### 4.2 Incremental Subsequent Audit (Workflow 2)
When `system-docs/seo-config.json` and `seo-tracker.json` ALREADY exist (or when user asks to fix SEO issues):
1. Agent loads `seo-config.json` and `seo-tracker.json` (verifying `schemaVersion`). Note: Live codebase is authoritative; past tracker status does not override current code inspection.
2. Agent scans current project routes, re-classifies each route through the multi-stage pipeline, calculates current `contentHash`, and computes `globalSeoHash`.
3. Agent checks for legacy tracker entries missing `contentHash`/classification or shared layout file modifications (`currentGlobalSeoHash != trackedGlobalSeoHash`).
4. Routes are classified into 4 distinct categories (`NEW`, `MODIFIED`, `UNCHANGED`, `DELETED`).
5. Confirmed `DELETED` routes are pruned from the active `routes` object in `seo-tracker.json`.
6. Only matching framework reference is loaded (e.g., `references/nextjs-react.md`).
7. Agent inspects live code for all **`NEW`** and **`MODIFIED`** routes where `isSeoPageCandidate: true`. Non-SEO candidates (`transactional`, `auth`, `utility`, `api`, `admin`, `redirect`, `asset`) are skipped. Checks Next.js 15+ async `params` where applicable.
8. Findings are classified in live code: `NEW ISSUE` (defect exists now), `ALREADY FIXED` (already compliant), `UNRESOLVED` (requires user input), `NO ISSUE` (clean).
9. Fixes are applied ONLY to actionable `NEW ISSUE` findings. On pass, record `Status: NEW ISSUE → FIXED` and add modified files to `Files Modified`; unchanged files are recorded as `Status: ALREADY FIXED → NO CHANGE` and added to `Files Unchanged`. Tracker `seo-tracker.json` is updated.
10. HTML sitemap is updated (or maintained as `"pending_domain"` if domain is UNRESOLVED).
11. **Validate Route Inventory Consistency & Category Count Reconciliation**: Confirm `verified SEO-page inventory == tracker SEO-page routes == sitemap candidates`. Dynamically compute category counts from `seo-tracker.json` routes and confirm strict count reconciliation: `sum(excluded category counts) == total excluded routes` and `seoPageCandidates + total excluded routes == total discovered routes` (e.g., 11 SEO + 8 API + 2 Auth/Admin + 1 Storage = 22 total). If any mismatch exists, halt execution or correct the calculation so all counts reconcile before proceeding.
12. **Regenerate / Update Deployment Guide**: Automatically execute Workflow 3 to regenerate or update `system-docs/DEPLOYMENT-SEO-GUIDE.md` reflecting the latest tracker state, resolved issues, and route changes.
13. **Report Progress & Conclude with Next Steps**: Present incremental progress report with findings lifecycle breakdown (`NEW ISSUE → FIXED`, `ALREADY FIXED → NO CHANGE`, `UNRESOLVED → USER INPUT REQUIRED`, `NO ISSUE`), non-SEO HTML routes & excluded route categories breakdown (with dynamically calculated counts reconciling with total routes), file modification breakdown, and conclude with the standardized `SEO AUDIT COMPLETE` Next Steps block.

---

## 5. Adding Future Framework Support

To add support for new frameworks (e.g., SvelteKit, Remix, Astro, Ruby on Rails):

1. **Do NOT rewrite `SKILL.md`**.
2. Create a new reference file under `02-seo/.agent/skills/seo/references/` (e.g., `sveltekit.md`).
3. Add framework detection signal to the detection table in `SKILL.md` pointing to `references/sveltekit.md`.
4. Document framework-specific route classification rules (pages vs API server handlers) and metadata APIs in the new reference file.
