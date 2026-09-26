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
2. **Discover first. Classify second. Audit third.** Never assume a route is an SEO page merely because it exists in the route list, is publicly reachable, or returns HTML (`responseType: html`). Enforce an explicit multi-stage classification pipeline: `route discovery -> response classification -> public/private classification -> SEO-page-candidate classification -> sitemap eligibility -> SEO audit`. A route is `isSeoPageCandidate: true` ONLY when implementation evidence proves it is a public, indexable, organic-search content/informational page (`routeType: 'page'`). Non-SEO HTML routes (transactional flows, auth/account access, post-action/utility pages) MUST be classified as `isSeoPageCandidate: false`.
3. **Ask before guessing business information.** Never invent target keywords, brand positioning, business claims, product descriptions, canonical domain choices, or ambiguous redirect targets. If information cannot be determined from code/docs/config, ask the user or insert explicit `TODO` markers.
4. **Detect before loading references.** Inspect the codebase to determine framework, router, rendering mode (`SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown`), and language (`JS`/`TS`/`PHP`). Load only the matching reference file on demand.
5. **Content-hash-based change detection.** Use deterministic cryptographic hashing (`SHA-256`) of route source content as the primary signal to determine if a route has changed. Do not rely solely on file modification timestamps (`mtime`).
6. **Shared SEO file invalidation.** Changes to shared SEO-affecting files (e.g. root layout `app/layout.tsx`, global head components, or framework SEO config) must invalidate affected routes so shared changes trigger re-audits.
7. **Legacy tracker backfilling & migration.** Trackers missing `contentHash` fields or classification metadata on existing routes must NOT be treated as `UNCHANGED`. They must be re-audited and backfilled with calculated hashes while maintaining `schemaVersion: "1.0"`.
8. **Explicit deleted-route cleanup.** Every incremental audit must compare discovered routes against previously tracked routes and remove entries for deleted routes.
9. **Conservative rendering detection.** Do not misclassify Next.js apps with `'use client'` components as pure `CSR`, or apps with `generateStaticParams` as pure `SSG`. Mark mixed route strategies as `Hybrid` and insufficient evidence as `Unknown`. Preserve `userOverridden: true` settings.
10. **Enforce JSON safety.** Always escape HTML characters when serializing structured data (e.g. JSON-LD `.replace(/</g, '\\u003c')`).
11. **Include `schemaVersion`.** Every generated `seo-config.json` and `seo-tracker.json` must include `"schemaVersion": "1.0"`.
12. **Canonical domain resolution & backend URL separation.** Never invent canonical domains or default to fallback URLs (`https://example.com`, `example.org`, `localhost`). Distinguish the production website origin from backend API base URLs (e.g. `NEXT_PUBLIC_BASE_URL=https://api.example.com/api/v1` or `http://backend.test/api/v1`), external service URLs, and local/dev URLs. A backend API URL must NEVER be promoted to the production website canonical domain. If the production website domain cannot be established from verified evidence (frontend env, site config, deployment docs), set `domain: "UNRESOLVED"` and ask the user.
13. **Sitemap generation deferral when domain is UNRESOLVED.** If `domain: "UNRESOLVED"`, DO NOT generate a physical production sitemap file containing absolute URLs with fabricated or fallback domains (`example.com`, `localhost`, backend domains). Record `globalStatus.sitemap: "pending_domain"`. Route inventory and sitemap eligibility analysis must still be completed. Concrete sitemap file generation is deferred until the domain is verified. Note: `sitemap: "pending_domain"` does NOT mean page SEO audit is incomplete (`status: "complete"` is independent of sitemap domain deferral).
14. **Standardized hierarchical metadata source tracking.** Use canonical field `metadataSource` with explicit independent property tracking:
    - `title: "page" | "inherited" | "missing"`
    - `description: "page" | "inherited" | "missing"`
    The Skill must strictly evaluate `title` and `description` independently (`resolveMetadataField(route, "title")` and `resolveMetadataField(route, "description")`) by inspecting the actual framework AST/exported metadata structure across the layout hierarchy (`page -> nearest applicable nested layout -> parent layout(s) -> root layout -> missing`):
    - **`page`**: The field is explicitly declared in page-level exported `metadata` or returned by page-level `generateMetadata()`. Page-level definitions take precedence over all parent layouts.
    - **`inherited`**: The field is absent from page level, BUT verified to be declared in an applicable parent layout (nearest nested layout takes precedence over grandparent/root layouts).
    - **`missing`**: The field is neither declared at page level NOR provided by any applicable parent layout in the chain.
    **Strict Prohibitions & Structural Standards**:
    - Prohibit raw substring/regex heuristics (`code.includes('title:')`, `code.includes('description:')`, `code.includes('generateMetadata')`). Metadata origin must be verified against actual exported `metadata` objects or AST returns.
    - NEVER infer `inherited` merely because a field is absent from the page file (must verify applicable parent layout actually supplies it).
    - NEVER infer `title: "page"` AND `description: "page"` merely because `generateMetadata()` exists (must inspect which fields `generateMetadata()` actually returns; resolve missing fields against parent hierarchy).
    - Never invent page-specific copy. If missing and unverified, report `UNRESOLVED → USER INPUT REQUIRED`.
15. **Next.js 15+ async `params` rule.** In Next.js 15+ App Router, `params` in `generateMetadata({ params })` is a `Promise` and MUST be `await`ed (`const { slug } = await params;`) before accessing properties. Flag direct unawaited access (`params.slug`, `params.id`) as a framework issue. Fix automatically when unambiguous.
16. **Dynamic route audit vs sitemap expansion.** Separate route component audit status from concrete sitemap expansion status (`complete`, `pending`, `unresolved`, `not-applicable`). A dynamic route template (`app/events/[id]/page.jsx`) can have `seoAudit: "complete"` if its code implementation passes, while its `sitemapExpansionStatus` is `unresolved` or `pending` if database/API records cannot be enumerated. Do NOT mark a dynamic route template as incomplete SEO merely because concrete sitemap URLs cannot be enumerated, and NEVER invent fake concrete URLs.
17. **CWV awareness vs measurement.** Static code inspections can verify implementation hygiene (`next/font`, `display: swap`, image dimensions, lazy loading) -> `cwvImplementation: "checked"`. Static analysis MUST NEVER infer `LCP PASS`, `CLS PASS`, `INP PASS`, or `Core Web Vitals PASS`. Without live lab/field measurement data, set `cwvMeasurement: "not_measured"` (`NOT MEASURED`). If measurement tools are unavailable, record `Validation: NOT RUN` (`Reason: Runtime/field measurement data unavailable`). Never turn static awareness into a performance score.
18. **Evidence-based validation.** Validation means executing actual checks (static syntax/JSON checks, framework typecheck/build/lint, or rendered HTML inspections). File existence alone is NOT validation. Every reported validation check must include `Command:`, `Result:`, `Evidence:`, `Impact:`. If validation could not be executed, report `Validation: NOT RUN` with a clear reason.
19. **Social identity safety.** If social account handles or production OG image paths are unverified, set them as `UNRESOLVED`. Never invent handle names (e.g. `@brandhandle`) or fake asset paths. Generic cards without handles may be used if valid, but prompt the user when handle or asset information is required.
21. **Explicit finding lifecycle & false-fix prohibition.** The Skill must distinguish the lifecycle of every audit finding: `NEW ISSUE`, `FIXED`, `ALREADY FIXED`, `UNRESOLVED`, and `NO ISSUE`. Whenever the Skill discovers a defect in the current source and successfully resolves it during the current run, the finding must explicitly report `Status: NEW ISSUE → FIXED` (it must NOT report only `FIXED`). The Skill must **NEVER** report an issue as `FIXED`, `NEW ISSUE → FIXED`, `FOUND & FIXED`, or equivalent unless the current run actually modified the relevant project file or configuration and post-fix validation confirms the issue is resolved. If the required implementation already exists in code, report `Status: ALREADY FIXED → NO CHANGE` with 0 files modified. If an issue cannot be safely resolved automatically (e.g. unknown domain), report `Status: UNRESOLVED → USER INPUT REQUIRED`. If inspected code is clean, report `Status: NO ISSUE`.
22. **Current-state precedence over historical tracker state.** Current source code is always authoritative for current implementation state. Historical tracker status is informational only. If a project is reverted between runs, re-detecting a defect is valid and must be reported as `NEW ISSUE → FIXED`. Conversely, if code already contains the correct implementation, report `ALREADY FIXED → NO CHANGE` regardless of past unresolved flags in tracker history.
23. **Separate tracking of inspected vs modified files.** The Skill must explicitly track and report `Files Inspected`, `Files Modified`, and `Files Unchanged`. A file must NEVER be listed as modified merely because it was inspected.

---

## Route Classification Framework

Every discovered route must be classified based on actual codebase implementation evidence (route files, controller response types, template rendering, middleware, and framework conventions) using a strict multi-stage classification pipeline:

```text
Route Discovery
  └── Response Classification (html, json, redirect, asset, unknown)
        └── Public / Private Access Determination (middleware, session guards, auth checks)
              └── Purpose & Route-Type Classification (page, transactional, auth, utility, api, admin, redirect, error, asset, unknown)
                    └── SEO Page Candidate Determination (isSeoPageCandidate: true ONLY for public 'page')
                          └── Sitemap Eligibility & Concrete URL Resolution
                                └── Targeted Page SEO Audit
```

### 1. Key Classification Principles
- **HTML Response Does NOT Equal SEO Page Candidate**: Serving an HTML document (`responseType: "html"`) is a necessary but INSUFFICIENT condition for SEO eligibility. Transactional booking/checkout steps, authentication screens, and post-action utility views render HTML but are NOT organic search candidates.
- **URL Path Patterns are Signals Only**: Path patterns such as `/api/*`, `/v1/*`, `/graphql`, `/admin/*`, `/dashboard/*`, `/auth/*`, `/book/*`, `/checkout/*` are indicative detection signals, NOT absolute proof. A route like `/v1/products` must NOT be classified as `api` based on URL prefix alone if the implementation renders an HTML document. Likewise, `/admin-guide` must NOT be classified as private if it is a public documentation page. Always inspect implementation evidence (controller/handler response, view rendering, forms, middleware).
- **Public/Private vs `robots.txt` Separation**: Public/Private classification is determined by authentication middleware, authorization guards, and session checks, NOT `robots.txt` rules or `noindex` tags. A public route may be `Disallow`ed in `robots.txt` for technical crawling reasons (e.g. search query pages), but remains a public page. A private route (`/dashboard`) remains private regardless of whether it appears in `robots.txt`. Do NOT use `robots.txt`, `noindex`, or sitemap tags as the sole evidence to classify route type.
- **Preservation of Public Informational/Content Pages**: Public informational pages (`/`, `/about`, `/events`, `/shop`, `/contact/careers`, `/contact/collaborate`, `/privacy`, `/terms`) and public dynamic detail templates (`/events/[id]`, `/products/[slug]`) are `routeType: "page"` and `isSeoPageCandidate: true`. Do NOT overcorrect and exclude legitimate public content.
- **Dynamic Route Templates vs Concrete URLs**: A dynamic route template (e.g., `/products/{slug}` in Laravel or `app/products/[slug]/page.tsx` in Next.js) is classified as a single dynamic page route template (`isSeoPageCandidate: true`). Do NOT invent concrete URL values (`/products/test-item`) in the tracker or sitemap unless actual database/content source evidence is inspected. Do NOT reclassify or remove a dynamic page route template as an error route merely because hypothetical or invalid parameter values return a 404 response. If data is unavailable, leave sitemap expansion unresolved or ask the user.

| Route Category | Description / Implementation Evidence Signals | `routeType` | `responseType` | `isSeoPageCandidate` | Sitemap Inclusion |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Public HTML Content Page** | Renders public HTML content/document intended for organic search discovery (`/`, `/about`, `/events`, `/events/[id]`, `/privacy`) | `page` | `html` | `true` | **Included** (Deferred if domain UNRESOLVED) |
| **Transactional Flow** | Booking, checkout, payment, confirmation, order completion, cancellation, cart actions (`/events/[id]/book`, `/checkout`) | `transactional` | `html` | `false` | **Excluded** |
| **Authentication / Access** | Login, register, forgot-password, reset-password, verify OTP, email verification (`/auth/login`, `/auth/register`) | `auth` | `html` | `false` | **Excluded** |
| **Post-Action & Utility View**| Success, confirmed, completed, status/health, action completion status screens (`/events/[id]/confirmed`, `/order/complete`) | `utility` | `html` | `false` | **Excluded** |
| **REST / JSON API** | Returns JSON, XML data, or API response (`return response()->json()`, `route.ts` API) | `api` | `json` | `false` | **Excluded** |
| **Admin / Private** | Protected by auth, admin, role, or session middleware (`/dashboard`, `/admin/settings`) | `admin` | `html` / `json` | `false` | **Excluded** |
| **Redirect Route** | Performs HTTP 301/302 redirects (`return redirect()`, `next.config.js` redirects) | `redirect` | `redirect` | `false` | **Excluded** |
| **Error / 404 View** | Exception handlers, 404 pages, 403/500 error templates (`not-found.tsx`, `error.tsx`) | `error` | `html` / `json` | `false` | **Excluded** |
| **Static Asset** | `.css`, `.js`, `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg`, `.ico`, `.pdf`, `.woff`, `.woff2` files | `asset` | `asset` | `false` | **Excluded** |
| **Unknown / Ambiguous**| Evidence is insufficient to determine response type, purpose, or public access | `unknown` | `unknown` | `false` (ask user) | **Excluded** |

### API-Backed Frontend Pages vs API Endpoints
When an application architecture uses a frontend document page (`/about`) that fetches data from a backend API endpoint (`/v1/cms-pages/about-us`):
- `/about` is the document page (`routeType: "page"`, `isSeoPageCandidate: true`).
- `/v1/cms-pages/about-us` is an API dependency (`routeType: "api"`, `isSeoPageCandidate: false`).
- SEO page-level audits (meta tags, canonicals, HTML JSON-LD, sitemaps) apply **only** to `/about`.
- API routes (including CMS APIs that return structured JSON content with titles, descriptions, or body markup) MUST NOT be included in HTML sitemaps or receive HTML page SEO audits merely because they are publicly accessible or return human-readable content fields. Sitemap inclusion requires `routeType: "page"` serving an actual user-facing document.

### Deterministic SEO Page Eligibility Rule
A route is classified as `isSeoPageCandidate: true` ONLY when ALL conditions are satisfied:
```text
isDocumentPage == true AND isPublic == true AND isRedirect == false AND isError == false AND isStaticAsset == false AND routeType == "page"
```
*Routes with `routeType` of `transactional`, `auth`, `utility`, `api`, `admin`, `redirect`, `error`, `asset`, or `unknown` are assigned `isSeoPageCandidate: false` and `status: "excluded"`.*

### Classification & Metadata Resolution Regression Scenarios (Cases 1–47)
The agent must verify route classification, structural metadata tracking, and audit behavior against these explicit benchmark cases:
- **Case 1 — Public HTML Page**: `/about` -> Renders HTML view, publicly accessible -> `routeType: "page"`, `responseType: "html"`, `isPublic: true`, `isSeoPageCandidate: true`.
- **Case 2 — REST/JSON API**: `/v1/cms-pages/about-us` -> Returns JSON response -> `routeType: "api"`, `responseType: "json"`, `isPublic: true`, `isSeoPageCandidate: false`.
- **Case 3 — API-Backed Frontend Page**: `/about` (HTML frontend document) consumes `/v1/cms-pages/about-us` (JSON API). `/about` is `isSeoPageCandidate: true`; `/v1/cms-pages/about-us` is `isSeoPageCandidate: false`.
- **Case 4 — Private/Authenticated Page**: `/dashboard` -> Protected by auth middleware -> `routeType: "admin"`, `isPublic: false`, `isSeoPageCandidate: false`.
- **Case 5 — Redirect Route**: `/old-about` -> Returns HTTP 301/302 redirect to `/about` -> `routeType: "redirect"`, `isRedirect: true`, `isSeoPageCandidate: false`.
- **Case 6 — Error / 404 Handler**: `/not-found` -> Serves 404 error template -> `routeType: "error"`, `isError: true`, `isSeoPageCandidate: false`.
- **Case 7 — Static Asset**: `/app.js` or `/font.woff2` -> Static asset -> `routeType: "asset"`, `isStaticAsset: true`, `isSeoPageCandidate: false`.
- **Case 8 — Ambiguous / Unknown Route**: `/something` -> Route definition exists but response type/access controls cannot be determined from code -> `routeType: "unknown"`, `isSeoPageCandidate: false`. Agent prompts user for clarification; NEVER guesses.
- **Case 9 — Dynamic Route Template**: `/products/{slug}` -> Dynamic page component template -> `routeType: "page"`, `isSeoPageCandidate: true`. Route template is tracked, but agent does NOT invent fake concrete URL instances (`/products/test-product`) without data source evidence. Testing invalid parameter values returning 404 does NOT cause route templates to be misclassified or removed.
- **Case 10 — Unknown Canonical Domain**: Production domain cannot be established from clear project evidence -> `domain: "UNRESOLVED"`, production canonical URLs and production OG image URLs are NOT authoritatively generated, user clarification is prompted when domain is required.
- **Case 11 — Inherited Metadata**: Root layout provides default title/description, page component defines no page-specific metadata -> `metadataSource: { title: "inherited", description: "inherited" }`. Inherited metadata is NOT reported as page-specific or automatically treated as an error; agent evaluates whether page-specific metadata is required without inventing copy.
- **Case 12 — Dynamic Route Without Concrete Data**: `/events/[id]` page component code passes SEO implementation audit -> `seoAudit: "complete"`. Concrete backend records cannot be queried -> `sitemapExpansionStatus: "unresolved"`. Route template remains tracked; NO fake concrete URLs are generated.
- **Case 13 — CWV Awareness vs Measurement**: Project implements `display: swap` or `next/font` -> `cwvImplementation: "checked"`. No Lighthouse/CrUX measurement run -> `cwvMeasurement: "not_measured"`. Agent NEVER claims `CWV PASS` without actual measurement data.
- **Case 14 — Unknown Social Identity**: Twitter/X handle or production OG image path is unverified -> `twitterHandle: "UNRESOLVED"`, `ogImage: "UNRESOLVED"`. Agent does NOT invent handle strings (e.g. `@brandhandle`) or fake asset paths; prompts user when handle/asset is required.
- **Case 15 — Shared SEO Change Invalidates Route**: Route component is unchanged, but shared root layout (`app/layout.tsx`) or global SEO config is modified -> `currentGlobalSeoHash != trackedGlobalSeoHash` -> Route is marked `MODIFIED` and re-audited.
- **Case 16 — Unresolved Domain Sitemap Deferral**: Production domain is `UNRESOLVED` -> No physical production sitemap containing absolute URLs with fabricated domains is created -> `globalStatus.sitemap: "pending_domain"`. Route inventory and eligibility checks complete; PASS only if no domain is invented.
- **Case 17 — Metadata Source Tracking**: Title inherited from parent layout, description explicitly declared on page -> `metadataSource: { title: "inherited", description: "page" }`. PASS only if title and description sources remain distinct.
- **Case 18 — Next.js 15+ Async Params**: Next.js 15+ App Router dynamic route uses `generateMetadata({ params })` with direct `params.slug` access -> Framework issue detected; Skill requires awaiting `params` (`const { slug } = await params;`).
- **Case 19 — Static CWV Without Measurement**: `next/font` and `display: swap` present without runtime/field performance measurement data -> `cwvImplementation: "checked"`, `cwvMeasurement: "not_measured"`. Validation reported as `NOT RUN` (Reason: Runtime/field measurement data unavailable); NEVER reports `CWV PASS`.
- **Case 20 — Backend API URL Is Not Website Domain**: Project config contains `NEXT_PUBLIC_BASE_URL=https://api.example.com/api/v1` or `http://backend.test/api/v1` without verified frontend site URL -> `domain: "UNRESOLVED"`, backend API URL tracked as API endpoint only. PASS only if backend API URL is NOT promoted to canonical website domain.
- **Case 21 — HTML Response Does NOT Imply SEO Candidate**: Route serves HTML (`responseType: "html"`) but implementation confirms transactional, auth, or utility purpose -> `isSeoPageCandidate: false`. PASS only if non-content HTML routes are excluded from SEO candidates.
- **Case 22 — Authentication Route Exclusion**: `/auth/login`, `/auth/register`, `/auth/forgot-password`, `/auth/reset-password`, `/auth/verify-otp` render HTML auth forms -> `routeType: "auth"`, `responseType: "html"`, `isSeoPageCandidate: false`. Excluded from sitemap and page SEO audit.
- **Case 23 — Transactional Route Exclusion**: `/events/[id]/book`, `/events/[id]/checkout`, `/events/[id]/confirmed` render HTML booking/checkout/confirmation steps -> `routeType: "transactional"` or `routeType: "utility"`, `responseType: "html"`, `isSeoPageCandidate: false`. Excluded from sitemap and page SEO audit.
- **Case 24 — Public Dynamic Content Remains SEO Eligible**: `/events/[id]` represents a public event detail content page -> `routeType: "page"`, `responseType: "html"`, `isPublic: true`, `isSeoPageCandidate: true`. Eligible for page SEO audit and sitemap inclusion (while concrete record expansion status remains independent).
- **Case 25 — Classification Evidence Beats URL Pattern**: A route whose path superficially resembles a content page (e.g. `/reserve`, `/action`) but whose implementation performs authenticated mutations, checkout steps, or account modifications is classified by implementation evidence as `transactional` or `auth` (`isSeoPageCandidate: false`), NOT as an SEO candidate.
- **Case 26 — NEW ISSUE → FIXED Lifecycle**: Defect genuinely detected in live codebase (e.g. unawaited `params` in Next.js 15+ `generateMetadata`) -> Skill modifies file -> post-fix validation passes -> Report `Finding: Next.js async params`, `Status: NEW ISSUE → FIXED`, `Action: Modified app/(main)/events/[id]/page.jsx`, `Validation: PASS`, file recorded in `Files Modified`.
- **Case 27 — Reverted Baseline Repeatability**: Target project is reverted to broken baseline between test runs -> Next run inspects live codebase -> defect is detected again as `Finding: Next.js async params`, `Status: NEW ISSUE → FIXED`. Live code state takes precedence over historical tracker state.
- **Case 28 — ALREADY FIXED → NO CHANGE Lifecycle**: Code inspected already contains the compliant implementation (e.g. `const { id } = await params;`) -> Skill makes 0 file modifications -> Report `Finding: Next.js async params`, `Status: ALREADY FIXED → NO CHANGE`, `Files Modified: 0`, file recorded in `Files Unchanged`. Never report "FOUND & FIXED" or only "FIXED".
- **Case 29 — UNRESOLVED vs NO ISSUE Lifecycle**: Issue detected but requires external info/credentials (e.g. production canonical domain) -> Report `Status: UNRESOLVED → USER INPUT REQUIRED`. Clean compliant area inspected with no defect -> Report `Status: NO ISSUE`.
- **Case 30 — Hierarchical Layout Resolution (Nearest Parent)**: Root layout provides `title`, intermediate nested layout `app/(main)/layout.jsx` provides `description`, page provides `title` -> For `/about`, `metadataSource: { title: "page", description: "inherited" }`. Inherited source is verified against nearest parent layout.
- **Case 31 — Dynamic Metadata Partial Returns**: Page `generateMetadata()` returns `{ title: "Event Detail" }` without `description` -> `metadataSource: { title: "page", description: "inherited" }` (if root/parent layout supplies `description`) or `metadataSource: { title: "page", description: "missing" }` (if no layout supplies it). NEVER classify both as `page` merely because `generateMetadata()` exists.
- **Case 32 — Parent Layout Uses generateMetadata**: Parent layout uses `generateMetadata()` to provide `title`/`description` -> Child page inherits them as `metadataSource: { title: "inherited", description: "inherited" }`.
- **Case 33 — Truly Missing Metadata**: Page defines neither field, and no applicable parent layout in hierarchy provides `description` -> `metadataSource.description: "missing"`. Requires actual proof across hierarchy that no parent supplies the field.
- **Case 34 — Unrelated String Property Rejection**: File contains unrelated object property `item.title = "Card"` or component prop `<Card title="..." />` but exports no Next.js metadata -> Prohibited from classifying as `metadataSource.title: "page"`.
- **Case 35 — Phase 3.6 Benchmark Verification**: Target project with explicit page-level metadata on all 9 SEO candidates -> `metadataSource: { title: "page", description: "page" }` for all 9 candidates, verified via actual exported metadata inspection.
- **Case 36 — Page Title + Inherited Description**: Page exports static `metadata.title`, parent layout exports `metadata.description` -> `metadataSource: { title: "page", description: "inherited" }`.
- **Case 37 — Inherited Title + Page Description**: Parent layout exports `metadata.title`, page exports static `metadata.description` -> `metadataSource: { title: "inherited", description: "page" }`.
- **Case 38 — Inherited Title + Inherited Description**: Page defines no metadata; root layout exports both `title` and `description` -> `metadataSource: { title: "inherited", description: "inherited" }`.
- **Case 39 — Missing Title + Page Description**: Page exports `metadata.description`, but neither page nor any parent layout in the chain exports `title` -> `metadataSource: { title: "missing", description: "page" }`.
- **Case 40 — Page Title + Missing Description**: Page exports `metadata.title`, but neither page nor any parent layout in the chain exports `description` -> `metadataSource: { title: "page", description: "missing" }`.
- **Case 41 — Both Fields Truly Missing**: Neither page nor any layout in the hierarchy exports `title` or `description` -> `metadataSource: { title: "missing", description: "missing" }`.
- **Case 42 — Nested Layout Overriding Root Layout**: Root layout exports `title: "Root Title"`, nested layout `app/(segment)/layout.jsx` exports `title: "Segment Title"`, child page defines no title -> Child resolves `title: "inherited"` from nearest nested layout (`"Segment Title"`).
- **Case 43 — Page Overriding Nested Layout**: Nested layout exports `title: "Segment Title"`, child page exports `title: "Page Title"` -> Child resolves `title: "page"` (`"Page Title"`), overriding parent layout.
- **Case 44 — Dynamic Metadata Returning Both Fields**: Page `generateMetadata()` returns `{ title: "Detail", description: "Summary" }` -> `metadataSource: { title: "page", description: "page" }`.
- **Case 45 — Dynamic Metadata Returning Only Title**: Page `generateMetadata()` returns `{ title: "Detail" }`, parent layout provides description -> `metadataSource: { title: "page", description: "inherited" }`.
- **Case 46 — Dynamic Metadata Returning Only Description**: Page `generateMetadata()` returns `{ description: "Summary" }`, parent layout provides title -> `metadataSource: { title: "inherited", description: "page" }`.
- **Case 47 — Dynamic Metadata Indeterminate Return**: `generateMetadata()` returns dynamic or conditional expression whose fields cannot be safely statically resolved -> Classify conservatively; prompt user when external data verification is required without inventing values.

---

## Finding Lifecycle & Audit Reporting Framework

The Skill separates detection, classification, remediation, validation, and reporting into explicit operational stages:

```text
Detect in Live Code
  └── Classify Finding (NEW ISSUE, ALREADY FIXED, UNRESOLVED, NO ISSUE)
        └── Determine Remediation Feasibility
              └── Modify Project Files (ONLY if actionable and needed)
                    └── Validate (Post-fix syntax / typecheck / render)
                          └── Report Status (NEW ISSUE → FIXED vs ALREADY FIXED → NO CHANGE vs UNRESOLVED → USER INPUT REQUIRED vs NO ISSUE)
```

### 1. Standardized Finding Lifecycle Statuses & Reporting

| Finding Condition | Action Taken | Explicit Status Reported | Current Run File Modification |
| :--- | :--- | :--- | :--- |
| **Defect Discovered & Fixed** | Automated fix applied & validated | **`Status: NEW ISSUE → FIXED`** | Relevant files modified; recorded in `Files Modified` |
| **Already Compliant in Code** | No modification required | **`Status: ALREADY FIXED → NO CHANGE`** | 0 files modified; recorded in `Files Unchanged` |
| **Defect/Config Needs Input** | Blocked on user input / external evidence | **`Status: UNRESOLVED → USER INPUT REQUIRED`** | 0 files modified; logged in pending list |
| **Clean Compliant Area** | Inspected area satisfies all rules | **`Status: NO ISSUE`** | 0 files modified; recorded in `Files Unchanged` |

### 2. Strict False-Fix & Status Naming Rules
1. **Discovered and Fixed**: Whenever the Skill discovers a defect in current source and resolves it during the run, it must report `Status: NEW ISSUE → FIXED`. It must **NOT** report only `FIXED`.
2. **False-Fix Prohibition**: The Skill must **NEVER** report an issue as `FIXED`, `NEW ISSUE → FIXED`, `FOUND & FIXED`, or equivalent unless:
   - The current run performed an actual modification to project files or configuration.
   - Post-fix validation evidence confirms the issue was resolved during the current run.
3. **Already Correct**: If the implementation was already compliant prior to the current run:
   ```text
   Status: ALREADY FIXED → NO CHANGE
   Files Modified: 0
   ```

### 3. Current-State vs Historical Tracker Precedence
- **Live Code Is Authoritative**: Current codebase inspection always overrides past tracker entries.
- **Deterministic Re-runs on Reverted Code**: If a customer repository is reverted to a broken baseline, the defect genuinely exists again. The Skill must re-detect it as `NEW ISSUE → FIXED`. Historical tracker notes of past fixes do not bypass live defect detection.
- **Repeatability on Already-Fixed Code**: If the code is already fixed, the Skill reports `Status: ALREADY FIXED → NO CHANGE`, even if past tracker entries had recorded the item as pending or newly fixed.

### 4. Separate Tracking of Inspected vs Modified Files
Audit reports must explicitly separate file interactions:
- **`Files Inspected`**: All project files analyzed during the audit.
- **`Files Modified`**: Only files actually written or modified during the current run.
- **`Files Unchanged`**: Inspected files where no modification was needed.

### 5. Standardized Finding Report Structure
```text
SEO Issues & Findings Breakdown:

1. [Issue Title / Rule Name]
   Status: [NEW ISSUE → FIXED | ALREADY FIXED → NO CHANGE | UNRESOLVED → USER INPUT REQUIRED | NO ISSUE]
   Action: [Modified <path> | NO CHANGE | USER INPUT REQUIRED | NONE]
   File(s): [Path(s) to affected file(s)]
   Validation: [PASS | FAIL | NOT RUN] (Evidence: ...)

Files Summary:
- Files Inspected: [Count / List]
- Files Modified: [Count / List] (0 if no changes made)
- Files Unchanged: [Count / List]
```

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
    "domain": "UNRESOLVED",
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
    "twitterHandle": "UNRESOLVED",
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
      "metadataSource": {
        "title": "page",
        "description": "page"
      },
      "sitemapExpansionStatus": "complete",
      "cwvImplementation": "checked",
      "cwvMeasurement": "not_measured",
      "lastAudited": "2026-09-25T10:00:00Z",
      "contentHash": "sha256:a1b2c3d4e5f67890123456789abcdef0123456789abcdef0123456789abcdef0",
      "issuesResolved": ["Added title and description", "Injected Organization JSON-LD"],
      "pendingIssues": []
    },
    "/events/[id]/book": {
      "routeType": "transactional",
      "responseType": "html",
      "isPublic": true,
      "isRedirect": false,
      "isSeoPageCandidate": false,
      "status": "excluded",
      "lastAudited": "2026-09-25T10:00:00Z",
      "contentHash": "sha256:b2c3d4e5f6a109876543210fedcba9876543210fedcba9876543210fedcba98",
      "issuesResolved": ["Excluded transactional flow from sitemap & SEO audit"],
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
    "totalRoutes": 3,
    "seoPageCandidates": 1,
    "completedRoutes": 1,
    "pendingRoutes": 0,
    "excludedRoutes": 2
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
3. **Ask Missing Essentials & Distinguish Domain vs API**: If production website domain or site name cannot be extracted from verified site config/env, prompt the user. Distinguish website domain from backend API base URLs (`NEXT_PUBLIC_BASE_URL`, `http://backend.test`); never promote backend API URLs to canonical website domains. If domain is unverified, set `domain: "UNRESOLVED"`.
4. **Initialize Config**: Create `system-docs/seo-config.json` with `"schemaVersion": "1.0"`. Preserve existing user overrides if present.
5. **Discover & Classify Routes**: Scan codebase to list all routes. Execute multi-stage classification pipeline:
   - Determine `responseType` (`html`, `json`, `redirect`, `asset`, `unknown`).
   - Determine `isPublic` (check auth guards/middleware; not robots.txt).
   - Classify `routeType` (`page`, `transactional`, `auth`, `utility`, `api`, `admin`, `redirect`, `error`, `asset`, `unknown`) based on implementation evidence (forms, mutations, auth flows, checkout logic, post-action states).
   - Assign `isSeoPageCandidate: true` ONLY for public `page` routes. Non-SEO HTML routes (`transactional`, `auth`, `utility`) receive `isSeoPageCandidate: false`.
6. **Build Verified SEO-Page Inventory**: Filter routes where `isSeoPageCandidate: true` (excludes non-SEO HTML routes, API endpoints, admin, redirects, error handlers, and static assets).
7. **Calculate Hashes**: Compute deterministic `sha256` content hash for each route and compute `globalSeoHash` for shared SEO layout files.
8. **Audit Global SEO**: Check sitemap configuration, `robots.txt`, root layout/head fallbacks, and global JSON-LD (`Organization`/`WebSite`).
9. **Audit Page SEO & Classify Findings**: Inspect **ONLY** verified `isSeoPageCandidate: true` routes in live code and classify each audit finding:
   - `<title>` tags and `<meta name="description">`: evaluate `resolveMetadataField(route, "title")` and `resolveMetadataField(route, "description")` independently across the hierarchy (`page -> nearest applicable nested layout -> parent layout(s) -> root layout -> missing`). Inspect actual AST/exported metadata structure and `generateMetadata()` return fields (never infer `inherited` or `page` from unverified presence/absence or naive substring matches). Report concrete evidence citing exact declarations.
   - Next.js 15+ App Router: verify `generateMetadata({ params })` awaits `params` (`const { slug } = await params;`) before property access. (If unawaited -> `NEW ISSUE`; if already awaited -> `ALREADY FIXED`)
   - Canonical URLs, Open Graph (`og:title`, `og:description`, `og:image`, `og:url`), and structured Data / JSON-LD
   - Heading hierarchy (`<h1>` uniqueness), image `alt` attributes, and internal linking
   - CWV static implementation hygiene (`cwvImplementation: "checked"`, `cwvMeasurement: "not_measured"`)
10. **Apply Safe Automated Fixes & Track File Modifications**:
    - For findings classified as `NEW ISSUE`, perform file modification ONLY if the fix is safe, unambiguous, and framework-compliant.
    - Validate changes immediately (syntax check, build, or typecheck). On pass, mark finding `Status: NEW ISSUE → FIXED`. Record modified file in `Files Modified`.
    - If the implementation is already compliant, mark `Status: ALREADY FIXED → NO CHANGE`, and record in `Files Unchanged`. NEVER claim "FOUND & FIXED" or only "FIXED".
    - For items requiring business input (unknown domain, social handles), mark `Status: UNRESOLVED → USER INPUT REQUIRED`.
11. **Generate HTML Sitemap (or Defer if Domain UNRESOLVED)**:
    - If `domain == "UNRESOLVED"`, DO NOT generate a physical production sitemap file containing absolute URLs with placeholder or backend domains (`example.com`, `localhost`). Record `globalStatus.sitemap: "pending_domain"`. Route inventory and candidate analysis are completed; physical sitemap generation is deferred until the domain is provided.
    - If `domain` is resolved, include **ONLY** verified `isSeoPageCandidate: true` routes in `sitemap.xml`. Exclude non-SEO HTML routes (`transactional`, `auth`, `utility`), API endpoints, redirects, admin routes, and static assets. Record `globalStatus.sitemap: "complete"`.
12. **Log Unresolved Items**: Mark items requiring business decisions as pending.
13. **Initialize Tracker**: Write findings to `system-docs/seo-tracker.json` with `"schemaVersion": "1.0"` including classification properties, `metadataSource`, and CWV status for all routes.
14. **Validate Route Inventory Consistency**: Ensure `verified SEO-page inventory == tracker SEO-page routes == sitemap candidates`. If any mismatch exists, halt execution and report an error immediately rather than proceeding.
15. **Report Status**: Present a structured summary with explicit breakdown of:
    - Discovered routes, SEO page candidates, non-SEO HTML routes (`transactional`, `auth`, `utility`), API routes, admin routes, redirects, error views, static assets.
    - Findings breakdown with lifecycle statuses (`NEW ISSUE → FIXED`, `ALREADY FIXED → NO CHANGE`, `UNRESOLVED → USER INPUT REQUIRED`, `NO ISSUE`).
    - File summary separating `Files Inspected`, `Files Modified`, and `Files Unchanged`.

---

## Workflow 2: Subsequent Incremental Audit

Perform this workflow when `system-docs/seo-config.json` and `system-docs/seo-tracker.json` ALREADY exist:

1. **Read Existing State**: Load `system-docs/seo-config.json` and `system-docs/seo-tracker.json`. Check `schemaVersion` compatibility. Respect any preserved `userOverridden` rendering settings. Note: Live source code is authoritative; historical tracker status does not override current code inspection.
2. **Scan & Classify Current Routes**: Discover current project routes, re-classify each route through the multi-stage pipeline, calculate current `contentHash`, and compute `globalSeoHash`.
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
7. **Execute Incremental Audit & Classify Findings**:
   - Inspect live code for all **`NEW`** and **`MODIFIED`** routes where `isSeoPageCandidate: true`, and re-check incomplete routes marked `needs_attention` or `pending`.
   - Skip non-SEO candidates (`transactional`, `auth`, `utility`, `api`, `admin`, `redirect`, `asset`).
   - Classify findings in live code: `NEW ISSUE` (defect exists now), `ALREADY FIXED` (already compliant), `UNRESOLVED` (blocked on user input), `NO ISSUE` (clean).
   - **Skip** `UNCHANGED` routes that are marked `complete`.
8. **Apply Fixes, Validate & Track Files**:
   - Modify files ONLY for actionable `NEW ISSUE` findings. Validate fixes. On pass, record `Status: NEW ISSUE → FIXED` and add to `Files Modified`.
   - For already compliant rules, record `Status: ALREADY FIXED → NO CHANGE`, and add to `Files Unchanged`. NEVER report "FOUND & FIXED" or only "FIXED" without current-run file modification.
   - Prompt user for unresolved business inputs (`Status: UNRESOLVED → USER INPUT REQUIRED`).
9. **Update Sitemap & Tracker**:
   - If `domain == "UNRESOLVED"`, keep/set `globalStatus.sitemap: "pending_domain"` without writing fake absolute URLs to disk.
   - If `domain` is resolved, regenerate `sitemap.xml` with verified `isSeoPageCandidate: true` routes.
   - Save updated `contentHash`, `globalSeoHash`, timestamps, and statuses to `system-docs/seo-tracker.json`.
10. **Validate Route Inventory Consistency**: Confirm `verified SEO-page inventory == tracker SEO-page routes == sitemap candidates`. If any mismatch exists, halt execution and report an error immediately rather than proceeding.
11. **Report Progress**: Present incremental changes, findings lifecycle breakdown (`NEW ISSUE → FIXED`, `ALREADY FIXED → NO CHANGE`, `UNRESOLVED → USER INPUT REQUIRED`, `NO ISSUE`), legacy backfilled routes, non-SEO HTML routes breakdown, remaining pending items, list of deleted routes removed, and `Files Inspected` vs `Files Modified` vs `Files Unchanged`.

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
