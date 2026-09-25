---
name: seo
description: >
  Autonomous SEO agent skill for auditing, fixing, and maintaining search engine and AI-search visibility
  across Next.js/React, Vue/Nuxt, Angular, and Laravel applications. Enforces formal route classification
  (HTML pages vs API endpoints), content hashing, shared file invalidation, rendering detection,
  and deleted-route cleanup via system-docs/seo-config.json and system-docs/seo-tracker.json.
---

# SEO Agent Skill

This skill provides an automated, framework-aware SEO audit, implementation, and tracking system for modern web projects. It operates portably across agent environments by storing project-specific state exclusively in the target repository's `system-docs/` folder.

---

## Core Rules & Guiding Principles

1. **Never hard-code project state into the reusable skill.** All audited state, route inventories, and configuration belong in the target project's `system-docs/` directory (`seo-config.json`, `seo-tracker.json`, `DEPLOYMENT-SEO-GUIDE.md`).
2. **Discover first. Classify second. Audit third.** Never assume a route is an SEO page merely because it exists in the route list or is publicly reachable. Inspect actual implementation evidence to classify routes (`page`, `api`, `admin`, `redirect`, `error`, `asset`, `unknown`) before auditing or generating sitemaps.
3. **Ask before guessing business information.** Never invent target keywords, brand positioning, business claims, product descriptions, canonical domain choices, or ambiguous redirect targets. If information cannot be determined from code/docs/config, ask the user or insert explicit `TODO` markers.
4. **Detect before loading references.** Inspect the codebase to determine framework, router, rendering mode (`SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown`), and language (`JS`/`TS`/`PHP`). Load only the matching reference file on demand.
5. **Content-hash-based change detection.** Use deterministic cryptographic hashing (`SHA-256`) of route source content as the primary signal to determine if a route has changed. Do not rely solely on file modification timestamps (`mtime`).
6. **Shared SEO file invalidation.** Changes to shared SEO-affecting files (e.g. root layout `app/layout.tsx`, global head components, or framework SEO config) must invalidate affected routes so shared changes trigger re-audits.
7. **Legacy tracker backfilling & migration.** Trackers missing `contentHash` fields or classification metadata on existing routes must NOT be treated as `UNCHANGED`. They must be re-audited and backfilled with calculated hashes while maintaining `schemaVersion: "1.0"`.
8. **Explicit deleted-route cleanup.** Every incremental audit must compare discovered routes against previously tracked routes and remove entries for deleted routes.
9. **Conservative rendering detection.** Do not misclassify Next.js apps with `'use client'` components as pure `CSR`, or apps with `generateStaticParams` as pure `SSG`. Mark mixed route strategies as `Hybrid` and insufficient evidence as `Unknown`. Preserve `userOverridden: true` settings.
10. **Enforce JSON safety.** Always escape HTML characters when serializing structured data (e.g. JSON-LD `.replace(/</g, '\\u003c')`).
11. **Include `schemaVersion`.** Every generated `seo-config.json` and `seo-tracker.json` must include `"schemaVersion": "1.0"`.

---

## Route Classification Framework

Every discovered route must be classified based on actual codebase implementation evidence (route files, controller response types, template rendering, middleware, and framework conventions):

### 1. Key Classification Principles
- **URL Path Patterns are Signals Only**: Path patterns such as `/api/*`, `/v1/*`, `/graphql`, `/admin/*`, `/dashboard/*` are indicative detection signals, NOT absolute proof. A route like `/v1/products` must NOT be classified as `api` based on URL prefix alone if the implementation renders an HTML document. Likewise, `/admin-guide` must NOT be classified as private if it is a public documentation page. Always inspect implementation evidence (controller/handler response, view rendering, middleware).
- **Public/Private vs `robots.txt` Separation**: Public/Private classification is determined by authentication middleware, authorization guards, and session checks, NOT `robots.txt` rules. A public route may be `Disallow`ed in `robots.txt` for technical crawling reasons (e.g. search query pages), but remains a public page. A private route (`/dashboard`) remains private regardless of whether it appears in `robots.txt`.
- **Dynamic Route Templates vs Concrete URLs**: A dynamic route template (e.g., `/products/{slug}` in Laravel or `app/products/[slug]/page.tsx` in Next.js) is classified as a single dynamic page route template (`isSeoPageCandidate: true`). Do NOT invent concrete URL values (`/products/test-item`) in the tracker or sitemap unless actual database/content source evidence is inspected. Do NOT reclassify or remove a dynamic page route template as an error route merely because hypothetical or invalid parameter values return a 404 response. If data is unavailable, leave sitemap expansion unresolved or ask the user.

| Route Category | Description / Evidence Signals | `routeType` | `responseType` | `isSeoPageCandidate` | Sitemap Inclusion |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Public HTML Page** | Renders HTML document/template (`return view()`, `.tsx` page, `.vue` page) intended for users | `page` | `html` | `true` | **Included** |
| **REST / JSON API** | Returns JSON, XML data, or API response (`return response()->json()`, `route.ts` API) | `api` | `json` | `false` | **Excluded** |
| **Admin / Private** | Protected by auth, admin, role, or session middleware | `admin` | `html` / `json` | `false` | **Excluded** |
| **Redirect Route** | Performs HTTP 301/302 redirects (`return redirect()`, `next.config.js` redirects) | `redirect` | `redirect` | `false` | **Excluded** |
| **Error / 404 View** | Exception handlers, 404 pages, 403/500 error templates | `error` | `html` / `json` | `false` | **Excluded** |
| **Static Asset** | `.css`, `.js`, `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg`, `.ico`, `.pdf`, `.woff`, `.woff2` files | `asset` | `asset` | `false` | **Excluded** |
| **Unknown / Ambiguous**| Evidence is insufficient to determine response type or public access | `unknown` | `unknown` | `false` (ask user) | **Excluded** |

### API-Backed Frontend Pages vs API Endpoints
When an application architecture uses a frontend document page (`/about`) that fetches data from a backend API endpoint (`/v1/cms-pages/about-us`):
- `/about` is the document page (`isSeoPageCandidate: true`).
- `/v1/cms-pages/about-us` is an API dependency (`isSeoPageCandidate: false`).
- SEO page-level audits (meta tags, canonicals, HTML JSON-LD, sitemaps) apply **only** to `/about`.
- API routes (including CMS APIs that return structured JSON content with titles, descriptions, or body markup) MUST NOT be included in HTML sitemaps or receive HTML page SEO audits merely because they are publicly accessible or return human-readable content fields. Sitemap inclusion requires `routeType: "page"` serving an actual user-facing document.

### Deterministic SEO Page Eligibility Rule
A route is classified as `isSeoPageCandidate: true` ONLY when ALL conditions are satisfied:
```text
isDocumentPage == true AND isPublic == true AND isRedirect == false AND isError == false AND isStaticAsset == false AND routeType == "page"
```
*Routes failing any condition are assigned `isSeoPageCandidate: false` and `status: "excluded"`.*

### Classification Regression Scenarios (Cases 1–9)
The agent must verify route classification against these explicit benchmark cases:
- **Case 1 — Public HTML Page**: `/about` -> Renders HTML view, publicly accessible -> `routeType: "page"`, `responseType: "html"`, `isPublic: true`, `isSeoPageCandidate: true`.
- **Case 2 — REST/JSON API**: `/v1/cms-pages/about-us` -> Returns JSON response -> `routeType: "api"`, `responseType: "json"`, `isPublic: true`, `isSeoPageCandidate: false`.
- **Case 3 — API-Backed Frontend Page**: `/about` (HTML frontend document) consumes `/v1/cms-pages/about-us` (JSON API). `/about` is `isSeoPageCandidate: true`; `/v1/cms-pages/about-us` is `isSeoPageCandidate: false`.
- **Case 4 — Private/Authenticated Page**: `/dashboard` -> Protected by auth middleware -> `routeType: "admin"`, `isPublic: false`, `isSeoPageCandidate: false`.
- **Case 5 — Redirect Route**: `/old-about` -> Returns HTTP 301/302 redirect to `/about` -> `routeType: "redirect"`, `isRedirect: true`, `isSeoPageCandidate: false`.
- **Case 6 — Error / 404 Handler**: `/not-found` -> Serves 404 error template -> `routeType: "error"`, `isError: true`, `isSeoPageCandidate: false`.
- **Case 7 — Static Asset**: `/app.js` -> Static JavaScript asset -> `routeType: "asset"`, `isStaticAsset: true`, `isSeoPageCandidate: false`.
- **Case 8 — Ambiguous / Unknown Route**: `/something` -> Route definition exists but response type/access controls cannot be determined from code -> `routeType: "unknown"`, `isSeoPageCandidate: false`. Agent prompts user for clarification; NEVER guesses.
- **Case 9 — Dynamic Route Template**: `/products/{slug}` -> Dynamic page component template -> `routeType: "page"`, `isSeoPageCandidate: true`. Route template is tracked, but agent does NOT invent fake concrete URL instances (`/products/test-product`) without data source evidence.

---

## Detection & Reference Mapping

When initialized, inspect the target repository to detect the project stack and rendering strategy:

| Framework / Stack | Signals | Primary Reference File | Supported Rendering Modes |
| :--- | :--- | :--- | :--- |
| **Next.js / React** | `next` in `package.json`, `app/` or `pages/` dirs, `vite.config.*` with React | [nextjs-react.md](references/nextjs-react.md) | `SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown` |
| **Vue / Nuxt** | `nuxt` or `vue` in `package.json`, `nuxt.config.*`, `pages/*.vue` | [vue-nuxt.md](references/vue-nuxt.md) | `SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown` |
| **Angular** | `angular.json`, `@angular/core` in `package.json` | [angular.md](references/angular.md) | `SSR`, `SSG`, `CSR`, `Unknown` |
| **Laravel** | `composer.json` containing `laravel/framework`, `routes/web.php` | [laravel.md](references/laravel.md) | `SSR`, `Hybrid`, `CSR`, `Unknown` |
| **Universal Concepts** | Core metadata, OG tags, JSON-LD, sitemap, robots, keywords | [core-seo-concepts.md](references/core-seo-concepts.md) | All |
| **Post-Deploy / GSC** | Deployment checklist, Google Search Console, Bing Webmaster Tools | [gsc-post-deploy.md](references/gsc-post-deploy.md) | All |

---

## Project State Architecture (`system-docs/`)

The skill reads and writes state in the target project's `system-docs/` folder:

### 1. `system-docs/seo-config.json`
Stores the project's SEO environment and settings.

```json
{
  "schemaVersion": "1.0",
  "project": {
    "name": "Project Name",
    "domain": "https://example.com",
    "framework": "nextjs",
    "router": "app-router",
    "language": "typescript",
    "rendering": "Hybrid",
    "renderingDetectedAutomatically": true,
    "detectedAutomatically": true,
    "userOverridden": false
  },
  "settings": {
    "defaultLocale": "en_US",
    "twitterHandle": "@sitehandle",
    "sitemapPath": "/sitemap.xml",
    "robotsPath": "/robots.txt"
  },
  "updatedAt": "2026-09-25T10:00:00Z"
}
```

### 2. `system-docs/seo-tracker.json`
Maintains an incremental inventory of audited routes, route classification metadata, content hashes, global SEO hash, and global status.

```json
{
  "schemaVersion": "1.0",
  "lastAuditTimestamp": "2026-09-25T10:00:00Z",
  "globalStatus": {
    "sitemap": "complete",
    "robots": "complete",
    "gscVerified": false,
    "cwvChecked": false,
    "globalSeoHash": "sha256:e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  },
  "routes": {
    "/about": {
      "routeType": "page",
      "responseType": "html",
      "isPublic": true,
      "isRedirect": false,
      "isSeoPageCandidate": true,
      "status": "complete",
      "lastAudited": "2026-09-25T10:00:00Z",
      "contentHash": "sha256:a1b2c3d4e5f67890123456789abcdef0123456789abcdef0123456789abcdef0",
      "issuesResolved": ["Added title and description", "Injected Organization JSON-LD"],
      "pendingIssues": []
    },
    "/v1/cms-pages/about-us": {
      "routeType": "api",
      "responseType": "json",
      "isPublic": true,
      "isRedirect": false,
      "isSeoPageCandidate": false,
      "status": "excluded",
      "lastAudited": "2026-09-25T10:00:00Z",
      "contentHash": "sha256:f6e5d4c3b2a109876543210fedcba9876543210fedcba9876543210fedcba98",
      "issuesResolved": ["Excluded from HTML sitemap & meta audit"],
      "pendingIssues": []
    }
  },
  "summary": {
    "totalRoutes": 2,
    "seoPageCandidates": 1,
    "completedRoutes": 1,
    "pendingRoutes": 0,
    "excludedRoutes": 1
  }
}
```

---

## Hashing, Invalidation & Migration Specifications

### 1. Deterministic Content Hashing
1. For each discovered route, identify its primary page source file(s) and directly associated layout/SEO components.
2. Include shared root layout / global SEO configuration files (e.g. `app/layout.tsx` in Next.js, `app.vue` in Nuxt, `resources/views/layouts/app.blade.php` in Laravel) in the route hash calculation or global SEO hash.
3. Construct deterministic string input by sorting file entries:
   ```text
   relative-file-path-1
   file-content-1
   relative-file-path-2
   file-content-2
   ```
4. Calculate the SHA-256 hash formatted as `sha256:<hex_digest>`.
5. **Hash Failure Handling**: If hash calculation fails for a route, mark it for re-audit. Never treat a failed hash calculation as `UNCHANGED`.
6. **Hash Integrity**: All hash entries in actual target project `seo-tracker.json` files MUST be real SHA-256 hashes calculated from source content (`sha256:<64 hex chars>`). The agent must NEVER output fake or placeholder hash strings (e.g. `sha256:a1b2c3...`) in actual project tracker files. Example hashes shown in documentation schemas are for illustration only.

### 2. Shared SEO File Invalidation
- The tracker records a `globalSeoHash` computed from shared SEO-affecting files (root layouts, site configuration, shared head components).
- During incremental audits, compute the current `globalSeoHash`.
- If `currentGlobalSeoHash != trackedGlobalSeoHash`, shared SEO configuration has changed. Mark all dependent routes as `MODIFIED` so they are re-audited.

### 3. Legacy Tracker Migration & Backfilling
- When reading `system-docs/seo-tracker.json`, if an existing route entry is missing `"contentHash"` or classification fields (`routeType`, `isSeoPageCandidate`):
  - Do **NOT** treat the route as `UNCHANGED`.
  - Mark the route for re-classification and baseline verification.
  - Compute current `contentHash` and classification properties.
  - Re-audit/verify the route, then backfill classification fields and `"contentHash"` into `seo-tracker.json`.
  - Maintain `"schemaVersion": "1.0"` (backward compatible field backfill).

---

## Workflow 1: Initial SEO Audit (First Run)

Perform this workflow when `system-docs/seo-config.json` does NOT exist:

1. **Inspect Project**: Scan `package.json`, `composer.json`, route files (`app/`, `pages/`, `routes/web.php`, `routes/api.php`), and configuration.
2. **Detect Stack, Language & Rendering**: Determine framework, router, rendering strategy (`SSR`/`SSG`/`CSR`/`Hybrid`/`Unknown`), and primary language conservatively. Record `renderingDetectedAutomatically`.
3. **Ask Missing Essentials**: If domain or site name cannot be extracted from config/env, prompt the user for target domain, site name, and brand details.
4. **Initialize Config**: Create `system-docs/seo-config.json` with `"schemaVersion": "1.0"`. Preserve existing user overrides if present.
5. **Discover & Classify Routes**: Scan codebase to list all routes. Classify each route into `page`, `api`, `admin`, `redirect`, `error`, `asset`, or `unknown`. Determine `isSeoPageCandidate`.
6. **Build Verified SEO-Page Inventory**: Filter routes where `isSeoPageCandidate: true`.
7. **Calculate Hashes**: Compute deterministic `sha256` content hash for each route and compute `globalSeoHash` for shared SEO layout files.
8. **Audit Global SEO**: Check sitemap configuration, `robots.txt`, root layout/head fallbacks, and global JSON-LD (`Organization`/`WebSite`).
9. **Audit Page SEO**: Inspect **ONLY** verified `isSeoPageCandidate: true` routes for:
   - `<title>` tags and `<meta name="description">`
   - Canonical URLs
   - Open Graph (`og:title`, `og:description`, `og:image`, `og:url`)
   - Structured Data / JSON-LD
   - Heading hierarchy (`<h1>` uniqueness)
   - Image `alt` attributes and internal linking
10. **Apply Safe Automated Fixes**: Update missing metadata, standard canonical tags, or correct structural formatting using framework conventions.
11. **Generate HTML Sitemap**: Include **ONLY** verified `isSeoPageCandidate: true` routes in `sitemap.xml`. Exclude API endpoints, redirects, admin routes, and static assets.
12. **Log Unresolved Items**: Mark items requiring business decisions as pending.
13. **Initialize Tracker**: Write findings to `system-docs/seo-tracker.json` with `"schemaVersion": "1.0"` including classification properties for all routes.
14. **Validate Route Inventory Consistency**: Ensure `verified SEO-page inventory == tracker SEO-page routes == sitemap candidates`. If any mismatch exists, halt execution and report an error immediately rather than proceeding.
15. **Report Status**: Present a structured summary of completed fixes, excluded API/admin routes, and pending items to the user.

---

## Workflow 2: Subsequent Incremental Audit

Perform this workflow when `system-docs/seo-config.json` and `system-docs/seo-tracker.json` ALREADY exist:

1. **Read Existing State**: Load `system-docs/seo-config.json` and `system-docs/seo-tracker.json`. Check `schemaVersion` compatibility. Respect any preserved `userOverridden` rendering settings.
2. **Scan & Classify Current Routes**: Discover current project routes, re-classify each route, calculate current `contentHash`, and compute `globalSeoHash`.
3. **Check Shared File Invalidation**: If `currentGlobalSeoHash != trackedGlobalSeoHash`, flag shared SEO changes.
4. **Compare & Classify Routes**: Compare current routes against tracked routes in `seo-tracker.json`:
   - **`NEW`**: Route discovered in code but absent from `seo-tracker.json`.
   - **`MODIFIED`**: Route exists in tracker, but `currentContentHash != trackedContentHash`, OR shared SEO files changed, OR route is missing `contentHash`/classification (legacy backfill).
   - **`UNCHANGED`**: Route exists in tracker, has valid `contentHash`, `currentContentHash == trackedContentHash`, and shared SEO files are unchanged.
   - **`DELETED`**: Route exists in `seo-tracker.json` `routes` map, but no longer exists in current discovered route inventory.
5. **Process Deleted Routes**:
   - Remove confirmed `DELETED` routes from the active `routes` map in `seo-tracker.json`.
   - Record deleted route paths to include in final report summary.
6. **Load Specific Reference**: Load only the framework reference specified in `seo-config.json`.
7. **Execute Incremental Audit**:
   - Audit all **`NEW`** and **`MODIFIED`** routes where `isSeoPageCandidate: true`.
   - Skip non-SEO candidates (`api`, `admin`, `redirect`, `asset`).
   - Re-check incomplete routes marked `needs_attention` or `pending`.
   - **Skip** `UNCHANGED` routes that are marked `complete`.
8. **Apply Fixes & Prompt**: Apply safe technical fixes; prompt user for unresolved business inputs.
9. **Update Sitemap & Tracker**: Regenerate `sitemap.xml` with verified `isSeoPageCandidate: true` routes. Save updated `contentHash`, `globalSeoHash`, timestamps, and statuses to `system-docs/seo-tracker.json`.
10. **Validate Route Inventory Consistency**: Confirm `verified SEO-page inventory == tracker SEO-page routes == sitemap candidates`. If any mismatch exists, halt execution and report an error immediately rather than proceeding.
11. **Report Progress**: Present incremental changes, newly audited routes, resolved issues, legacy backfilled routes, remaining pending items, and list any deleted routes removed from tracking.

---

## Workflow 3: Deployment Guide Generation

When the user requests a deployment or go-live checklist, generate `system-docs/DEPLOYMENT-SEO-GUIDE.md`:

1. Read current state from `system-docs/seo-tracker.json`.
2. Load static post-deployment rules from [gsc-post-deploy.md](references/gsc-post-deploy.md).
3. Combine tracker status with step-by-step instructions for:
   - Google Search Console domain verification & sitemap submission
   - Bing Webmaster Tools setup
   - Verification of indexability and robots.txt rules
   - Core Web Vitals and post-launch monitoring setup
4. Save the customized document to `system-docs/DEPLOYMENT-SEO-GUIDE.md`.

---

## SEO Scope (Version 1)

Focus on high-impact, practical SEO capabilities:

- **Core Metadata**: Title, description, canonicals, robots index/noindex, Open Graph, Twitter Cards.
- **Structured Data**: JSON-LD schemas (`Organization`, `WebSite`, `LocalBusiness`, `Article`, `Product`, `BreadcrumbList`) with XSS escaping.
- **Crawl & Indexability**: Framework-native `sitemap.xml` and `robots.txt` generation.
- **Page Optimization**: Heading hierarchy (`<h1>` checks), image `alt` attributes, basic URL structure, internal link integrity.
- **Technical Hygiene**: Canonical conflict resolution, basic redirect validation, 404 metadata safety.
- **Post-Deploy Readiness**: Search console integration steps and monitoring checklists.
