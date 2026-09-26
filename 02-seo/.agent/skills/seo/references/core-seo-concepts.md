# Core SEO Concepts & Principles

This reference details universal SEO standards applicable across all web applications regardless of framework.

---

## 1. Route Classification & Sitemap Eligibility

Before performing SEO audits or generating sitemaps, every route must undergo formal classification following the **"Discover first. Classify second. Audit third."** principle across an explicit 6-stage pipeline:

```text
Route Discovery
  └── Response Classification (html, json, redirect, asset, unknown)
        └── Public / Private Access Determination (middleware, session guards, auth checks)
              └── Purpose & Route-Type Classification (page, transactional, auth, utility, api, admin, redirect, error, asset, unknown)
                    └── SEO Page Candidate Determination (isSeoPageCandidate: true ONLY for public 'page')
                          └── Sitemap Eligibility & Concrete URL Resolution
                                └── Targeted Page SEO Audit
```

### 1.1 Core Classification Principles
1. **HTML Response Does NOT Equal SEO Candidate**: Serving an HTML document (`responseType: "html"`) does NOT automatically make a route an SEO candidate. Transactional booking/checkout steps, authentication screens, and post-action utility views render HTML but are NOT organic search targets.
2. **URL Path Patterns are Signals Only**: Path patterns like `/v1/*`, `/api/*`, `/graphql`, `/admin/*`, `/dashboard/*`, `/auth/*`, `/book/*`, `/checkout/*` are indicative detection signals, NOT absolute proof. A route like `/v1/products` must NOT be assumed to be an API endpoint if the code actually renders an HTML document. Likewise, a route like `/account/setup` must NOT be assumed to be a public content page if it performs authenticated user onboarding. Always verify actual implementation evidence (controller/handler response, view rendering, forms, mutations, middleware).
3. **Public/Private vs `robots.txt` Separation**: Public/Private status depends on authentication and authorization middleware (session checks, role guards, login redirects), NOT `robots.txt` directives or `noindex` tags. A public page may be disallowed in `robots.txt` for technical indexing reasons (e.g. search query endpoints), but remains a public page. Conversely, a private page (`/dashboard`) remains private regardless of `robots.txt` entries. Do NOT use `robots.txt`, `noindex`, or sitemap tags as the sole evidence for determining what a route is.
4. **Preservation of Public Content Pages**: Public informational and content pages (`/`, `/about`, `/events`, `/shop`, `/contact/careers`, `/contact/collaborate`, `/privacy`, `/terms`) and dynamic content templates (`/events/[id]`, `/products/[slug]`) remain `routeType: "page"` and `isSeoPageCandidate: true`. Do NOT overcorrect and exclude legitimate public content.
5. **Dynamic Route Templates vs Concrete URLs**: A dynamic route pattern (`/products/{slug}`) represents a page route template (`isSeoPageCandidate: true`). The agent must NOT invent fake concrete URLs (`/products/test-product`) in the sitemap or tracker without inspecting actual content/database data sources. Do NOT reclassify or remove a dynamic page route template as an error route merely because invalid parameter values in hypothetical URLs return a 404 response.

### 1.2 Classification Criteria & Categories
1. **Public HTML Content Pages (`routeType: "page"`, `responseType: "html"`, `isSeoPageCandidate: true`)**:
   - Renders a public user-facing document intended for organic search discovery.
   - Examples: Home, About Us, Events directory, Blog posts, Product detail pages (`/events/[id]`), Policy pages (`/privacy`, `/terms`).
   - **Action**: Included in HTML `sitemap.xml` (deferred if domain UNRESOLVED); audited for metadata, canonicals, OG tags, JSON-LD, headings, images, and links.
2. **Transactional / Conversion Flows (`routeType: "transactional"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
   - Interactive transactional user journeys such as ticket booking, checkout, payment processing, cart mutations, and step wizards.
   - Examples: `/events/[id]/book`, `/events/[id]/checkout`, `/cart/payment`, `/order/step-2`.
   - **Action**: **Excluded** from HTML `sitemap.xml`; **Excluded** from page SEO audits.
3. **Authentication & Account Access (`routeType: "auth"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
   - Login, registration, password recovery, OTP verification, and account verification screens.
   - Examples: `/auth/login`, `/auth/register`, `/auth/forgot-password`, `/auth/reset-password`, `/auth/verify-otp`.
   - **Action**: **Excluded** from HTML `sitemap.xml`; **Excluded** from page SEO audits.
4. **Post-Action & Utility Views (`routeType: "utility"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
   - Temporary confirmation screens, success notices, cancelled transaction states, and health/status check views.
   - Examples: `/events/[id]/confirmed`, `/checkout/success`, `/booking/cancelled`, `/healthz`.
   - **Action**: **Excluded** from HTML `sitemap.xml`; **Excluded** from page SEO audits.
5. **REST / JSON API Endpoints (`routeType: "api"`, `responseType: "json"`, `isSeoPageCandidate: false`)**:
   - Endpoints returning JSON, XML data, or API payloads (`/api/*`, `/v1/*`, `/graphql`).
   - *CMS API Nuance*: API routes (including CMS APIs returning structured JSON with title/description/body markup) MUST NOT be included in HTML sitemaps or receive HTML page SEO audits merely because they are publicly reachable or contain human-readable content fields. Sitemap inclusion requires `routeType: "page"` serving an actual user-facing document.
   - **Action**: **Excluded** from HTML `sitemap.xml`; **No** HTML metadata or canonical audit.
6. **API-Backed Frontend Pages**:
   - When a frontend document page (`/about`) fetches content from a backend API endpoint (`/v1/cms-pages/about-us`), `/about` is the SEO page candidate (`routeType: "page"`, `isSeoPageCandidate: true`), and `/v1/cms-pages/about-us` is its API dependency (`routeType: "api"`, `isSeoPageCandidate: false`).
7. **Admin / Private Routes (`routeType: "admin"`, `isSeoPageCandidate: false`)**:
   - Protected by authentication or authorization middleware (`auth`, `admin`, session guards).
   - Examples: `/dashboard`, `/admin/users`, `/settings/billing`.
   - **Action**: **Excluded** from `sitemap.xml`; disallowed in `robots.txt` if private.
8. **Redirects & Error Views (`routeType: "redirect"` / `routeType: "error"`, `isSeoPageCandidate: false`)**:
   - HTTP 301/302 redirects and 404/500 error pages.
   - **Action**: **Excluded** from HTML `sitemap.xml`.
9. **Static Assets (`routeType: "asset"`, `isSeoPageCandidate: false`)**:
   - `.css`, `.js`, `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg`, `.ico`, `.pdf`, `.woff`, `.woff2` files.
   - **Action**: **Excluded** from HTML `sitemap.xml`.
10. **Unknown / Ambiguous Routes (`routeType: "unknown"`, `isSeoPageCandidate: false`)**:
    - Routes where implementation evidence is insufficient to determine response type or access level.
    - **Action**: Classified as `routeType: "unknown"`, `isSeoPageCandidate: false`, ask user; NEVER guess.

### 1.3 Benchmark Regression Cases (Cases 1–47)
- **Case 1 — Public HTML Page**: `/about` -> Renders HTML document -> `isSeoPageCandidate: true` -> Included in sitemap.
- **Case 2 — JSON API Endpoint**: `/v1/cms-pages/about-us` -> Returns JSON data -> `isSeoPageCandidate: false` -> Excluded from sitemap.
- **Case 3 — API-Backed Frontend Page**: `/about` (frontend HTML page) fetches `/v1/cms-pages/about-us` (backend JSON API). `/about` is `isSeoPageCandidate: true`; `/v1/cms-pages/about-us` is `isSeoPageCandidate: false`.
- **Case 4 — Private/Authenticated Page**: `/dashboard` -> Requires login session/guard -> `isPublic: false`, `isSeoPageCandidate: false` -> Excluded.
- **Case 5 — Redirect Route**: `/old-about` -> Returns HTTP 301/302 redirect to `/about` -> `isRedirect: true`, `isSeoPageCandidate: false` -> Excluded.
- **Case 6 — Error / 404 Handler**: `/not-found` -> Serves 404 page template -> `isError: true`, `isSeoPageCandidate: false` -> Excluded.
- **Case 7 — Static Asset**: `/app.js` or `/font.woff2` -> Static asset -> `isStaticAsset: true`, `isSeoPageCandidate: false` -> Excluded.
- **Case 8 — Ambiguous Route**: `/something` -> Implementation details unclear -> `routeType: "unknown"`, `isSeoPageCandidate: false` -> Prompt user.
- **Case 9 — Dynamic Route Template**: `/products/{slug}` -> Dynamic page route template -> `isSeoPageCandidate: true`. Do NOT invent fake URLs (`/products/sample`). Testing invalid parameter 404 responses does NOT remove route template.
- **Case 10 — Unknown Canonical Domain**: Production domain cannot be established from clear evidence -> `domain: "UNRESOLVED"`, production canonical URLs and production OG URLs are NOT authoritatively generated, user clarification is prompted.
- **Case 11 — Inherited Metadata**: Root layout provides default title/description, page defines no page-specific metadata -> `metadataSource: { title: "inherited", description: "inherited" }`. Inherited metadata is NOT reported as page-specific or automatically treated as an error; agent evaluates whether page-specific metadata is needed without inventing copy.
- **Case 12 — Dynamic Route Without Concrete Data**: `/events/[id]` page component code passes SEO audit -> `seoAudit: "complete"`. Concrete backend records cannot be queried -> `sitemapExpansionStatus: "unresolved"`. Route template remains tracked; NO fake concrete URLs are generated.
- **Case 13 — CWV Awareness vs Measurement**: Project implements `display: swap` or `next/font` -> `cwvImplementation: "checked"`. No Lighthouse/CrUX measurement run -> `cwvMeasurement: "not_measured"`. Agent NEVER claims `CWV PASS` without actual measurement data.
- **Case 14 — Unknown Social Identity**: Twitter/X handle or production OG image path is unverified -> `twitterHandle: "UNRESOLVED"`, `ogImage: "UNRESOLVED"`. Agent does NOT invent handle strings (e.g. `@brandhandle`) or fake asset paths; prompts user when required.
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
- **Case 26 — NEW ISSUE → FIXED Lifecycle**: Defect genuinely detected in live codebase -> Skill modifies file -> post-fix validation passes -> Report `Finding: Next.js async params`, `Status: NEW ISSUE → FIXED`, `Action: Modified app/(main)/events/[id]/page.jsx`, `Validation: PASS`, file recorded in `Files Modified`.
- **Case 27 — Reverted Baseline Repeatability**: Target project is reverted to broken baseline between test runs -> Next run inspects live codebase -> defect is detected again as `Finding: Next.js async params`, `Status: NEW ISSUE → FIXED`. Live code state takes precedence over historical tracker state.
- **Case 28 — ALREADY FIXED → NO CHANGE Lifecycle**: Code inspected already contains the compliant implementation -> Skill makes 0 file modifications -> Report `Finding: Next.js async params`, `Status: ALREADY FIXED → NO CHANGE`, `Files Modified: 0`, file recorded in `Files Unchanged`. Never report "FOUND & FIXED" or only "FIXED".
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

## 2. Title Tags & Meta Descriptions

### 2.1 Title Tags (`<title>`)
- **Length**: Keep titles between **50–60 characters** (approx. 580 pixels) to prevent truncation in SERPs.
- **Format**: `Page Primary Keyword - Secondary Keyword | Brand Name`
- **Rules**:
  - Every indexable page must have a unique title tag.
  - Front-load primary keywords near the start of the title.
  - Avoid keyword stuffing.

### 2.2 Meta Descriptions
- **Length**: Keep descriptions between **140–160 characters**.
- **Rules**:
  - Include a clear call-to-action (CTA) and relevant target keywords.
  - Unique per page; do not reuse default site descriptions across subpages.

### 2.3 Standardized Hierarchical Metadata Source Tracking (`metadataSource`)
The skill tracks metadata origin using the canonical field name `metadataSource` (never invent competing field names like `metadataOrigin`). It tracks `title` and `description` independently:

```yaml
metadataSource:
  title: page | inherited | missing
  description: page | inherited | missing
```

#### 2.3.1 Hierarchical Resolution Model & Traversal Algorithm
Metadata source resolution must follow the actual framework metadata inheritance hierarchy:

```text
Inspect Page File (metadata / generateMetadata)
  ├── Field explicitly defined in page?
  │     └── YES -> Source: "page" (Page definition takes precedence over layouts)
  │
  └── NO -> Traverse Parent Layouts (Nearest Nested Layout -> ... -> Root Layout)
        ├── Field explicitly defined in applicable parent layout?
        │     └── YES -> Source: "inherited" (from nearest applicable layout)
        │
        └── NO -> No applicable page or parent layout defines the field
              └── Source: "missing"
```

1. **`page` (Page-Specific)**:
   - The field is explicitly declared in page-level exported `metadata` or explicitly returned by page-level `generateMetadata()`.
   - Page-level definition always overrides parent and root layout declarations.
2. **`inherited` (Inherited from Parent Layout)**:
   - The field is NOT defined at page level, BUT is verified to be declared in an applicable parent layout (nested layout or root layout) in the route's hierarchy.
   - The effective source is the **nearest applicable parent layout** that declares the field (e.g. `app/(segment)/layout.jsx` overrides `app/layout.jsx`).
3. **`missing` (Truly Missing)**:
   - The field is neither declared at page level NOR provided by any applicable parent layout in the route's layout chain.

#### 2.3.2 Field-Level Independent Resolution Algorithm
`title` and `description` must always be resolved independently using separate evaluators:
```text
resolveMetadataField(route, "title")       -> "page" | "inherited" | "missing"
resolveMetadataField(route, "description") -> "page" | "inherited" | "missing"
```
Never use logic equivalent to `if (pageHasMetadata) metadataSource = "page"` because a page can define only one field while inheriting the other:
- **Static Both**: Page defines both -> `title: "page", description: "page"`.
- **Partial Static (Title only)**: Page defines title, parent defines description -> `title: "page", description: "inherited"`.
- **Partial Static (Description only)**: Page defines description, parent defines title -> `title: "inherited", description: "page"`.
- **Inherited Both**: Page defines neither, root/parent defines both -> `title: "inherited", description: "inherited"`.
- **Missing One**: Page defines title, no layout defines description -> `title: "page", description: "missing"`.
- **Missing Both**: Neither page nor any layout in chain defines either field -> `title: "missing", description: "missing"`.

#### 2.3.3 Dynamic Metadata (`generateMetadata`) Structural Inspection
- If a page defines `export async function generateMetadata()`, inspect its actual returned properties field-by-field:
  - Returns `{ title: "..." }` only -> `title: "page"`, `description: "inherited"` (if parent supplies it) or `"missing"`.
  - Returns `{ description: "..." }` only -> `title: "inherited"` (if parent supplies it) or `"missing"`, `description: "page"`.
  - Returns `{ title: "...", description: "..." }` -> `title: "page"`, `description: "page"`.
- **STRICT PROHIBITION**: NEVER assume that the mere presence of `generateMetadata` means both `title` and `description` are `page`.
- **Indeterminate Returns**: If `generateMetadata()` returns dynamic or conditional expressions that cannot be safely statically resolved, classify the field conservatively and prompt the user if verification is required without inventing values.
- If an applicable parent layout defines `generateMetadata()`, any fields it returns are treated as `inherited` by child routes.

#### 2.3.4 Prohibition on Naive String Heuristics
The Skill must strictly inspect actual framework metadata AST / exported object structures. Simplistic string matches such as:
- `code.includes('title:')`
- `code.includes('description:')`
- `code.includes('generateMetadata')`
are INSUFFICIENT proof and must NOT be used as the authoritative validation mechanism. False positives from UI text, component props (`<Card title="..." />`), data objects, or comments must be rejected.

#### 2.3.5 Metadata Source Evidence & Validation Reporting Standard
Validation reports must provide concrete evidence citing exact declarations for each field independently:

```text
Route: /about
Title source: page
Description source: inherited
Evidence:
  page.jsx → exports metadata.title ("About Us")
  page.jsx → does not define description
  nearest applicable layout (app/(main)/layout.jsx) → exports metadata.description ("Company Overview")
```

```text
Route: /events/[id]
Title source: page
Description source: page
Evidence:
  generateMetadata() return object contains title
  generateMetadata() return object contains description
```

```text
Route: /terms
Title source: page
Description source: missing
Evidence:
  page.jsx → exports metadata.title ("Terms of Service")
  page.jsx → does not define description
  applicable layouts do not define description
  root layout does not define description
```

---

## 3. Canonical URLs, Domain Resolution & Sitemap Deferral

### 3.1 Distinguishing Production Website Domain from Backend API Base URL
- **Production Website Domain**: The public-facing origin where user HTML pages are hosted (e.g. `https://mybrand.com`).
- **Backend API Base URL**: The endpoint origin where REST/GraphQL data services reside (e.g. `NEXT_PUBLIC_BASE_URL=https://api.example.com/api/v1` or `http://backend.test/api/v1`).
- **External / Dev URLs**: Local development servers (`http://localhost:3000`, `http://127.0.0.1:8000`), staging URLs, or third-party webhooks.
- **Strict Separation Rule**: A backend API URL must **NEVER** be promoted to or assumed as the production website canonical domain.
- **Evidence Requirement**: Establishing the production canonical domain requires authoritative evidence representing the public website origin (e.g. explicit site URL environment variable `NEXT_PUBLIC_SITE_URL`, documented deployment domain, or explicit user confirmation). If evidence is insufficient, set `domain: "UNRESOLVED"` and ask the user.

### 3.2 Sitemap Generation Deferral When Domain is UNRESOLVED
If the production website domain is `UNRESOLVED`:
1. **DO NOT** generate a physical production sitemap (`sitemap.xml` / `sitemap.ts`) containing absolute URLs with fabricated or fallback domains (`https://example.com`, `https://example.org`, `http://localhost`, or backend API URLs).
2. Record `globalStatus.sitemap: "pending_domain"`.
3. Complete the route inventory and sitemap eligibility analysis (`isSeoPageCandidate: true`).
4. Only the **concrete physical sitemap file writing** is deferred until the domain is confirmed.
5. **Separation of Page SEO Audit & Sitemap Generation**: A page component can have `status: "complete"` and `seoAudit: "complete"` while sitemap generation remains deferred (`globalStatus.sitemap: "pending_domain"`). Do NOT interpret sitemap domain deferral as an incomplete page SEO audit.

### 3.3 Canonical Tag Formatting
- **Format**: Always use absolute URLs with protocol (`https://`).
- **Trailing Slashes**: Maintain site-wide consistency (`https://example.com/about` vs `https://example.com/about/`).
- **Self-Referencing Canonicals**: Clean URLs without query parameters (`?utm_source=...`, `?ref=...`).

---

## 4. Open Graph & Social Metadata Safety

### Minimum Required OG Tags
```html
<meta property="og:title" content="Page Title — Brand Name" />
<meta property="og:description" content="Engaging summary under 160 characters." />
<meta property="og:url" content="https://example.com/page-path" />
<meta property="og:image" content="https://example.com/images/og-cover.png" />
<meta property="og:type" content="website" />
<meta property="og:site_name" content="Brand Name" />
```

### Social Identity & Asset Safety Rules
- **Unverified Handles**: If Twitter/X account handles or production OG image paths are unverified in project code, set `twitterHandle: "UNRESOLVED"` and `ogImage: "UNRESOLVED"`.
- **No Fabricated Handles**: NEVER invent handles (e.g. `@brandhandle`, `@sitehandle`) or fake asset paths (`/images/og-cover.png`). Generic Twitter cards without site handles may be used if valid, but prompt the user when account handle or production OG image is required.

---

## 5. Structured Data (JSON-LD)

### Critical Safety Rule (XSS Prevention)
When embedding JSON-LD inside HTML `<script>` tags, **always escape `<` characters** to prevent script tag injection vulnerabilities:

```javascript
const safeJsonLd = JSON.stringify(schemaObject).replace(/</g, '\\u003c');
```

---

## 6. Heading Hierarchy & On-Page Structure

1. **Single `<h1>`**: Exactly one `<h1>` per page containing the primary page subject/title.
2. **Logical Nesting**: `<h1>` -> `<h2>` -> `<h3>` -> `<h4>`. Never skip levels.
3. **Image Alt Attributes**: All informative images must include descriptive `alt` text.
4. **Link Text**: Use descriptive anchor text ("View our pricing plans") instead of generic phrases ("click here").

---

## 7. Placeholder & Unknown Information Rules

- **Ask Before Guessing**: Never invent company registration details, phone numbers, target keywords, canonical domains, or social media handles.
- **TODO Marking**: If automated execution requires proceeding without user input, populate placeholders with clear comment flags:

```html
<!-- TODO (SEO): Update meta description with verified target keyword strategy -->
<meta name="description" content="TODO: Add product description for Service X" />
```

---

## 8. Content Hash Integrity & Invalidation Rules

1. **Strict SHA-256 Formatting**: Content hashes in actual project `seo-tracker.json` files MUST be computed 64-character hexadecimal SHA-256 hashes (`sha256:<64 hex chars>`).
2. **No Placeholders**: Never write placeholder or fake hashes (such as `sha256:a1b2c3...`) into a target project's `seo-tracker.json`. Example hash strings in documentation schemas are for format illustration only.
3. **Determinism**: Hash inputs are created by sorting relative source file paths and combining them with file contents.
4. **Invalidation**: Changes to root layouts update `globalSeoHash`, triggering re-audits for all dependent routes (`currentGlobalSeoHash != trackedGlobalSeoHash`).

---

## 9. Core Web Vitals (CWV): Awareness vs Measurement

1. **CWV Implementation Awareness**: Static code inspections verifying font strategies (`display: swap`, `next/font`), image optimization (`next/image`, `loading="lazy"`, explicit `width`/`height`), layout stability, and resource hints. Status: `cwvImplementation: "checked"`.
2. **CWV Actual Measurement**: Quantitative performance scoring using Lighthouse, PageSpeed Insights API, Chrome User Experience Report (CrUX), or Real User Monitoring (RUM). Status: `cwvMeasurement: "measured"` or `cwvMeasurement: "not_measured"`.
3. **Prohibition on Overclaiming**: Static analysis **MUST NEVER** infer `LCP PASS`, `CLS PASS`, `INP PASS`, or `Core Web Vitals PASS`. Without active runtime or lab measurement data, report `cwvMeasurement: "not_measured"` (`NOT MEASURED`).
4. **Validation Reporting**: If measurement tooling is unavailable, record:
   ```text
   Validation: NOT RUN
   Reason: Runtime/field measurement data unavailable
   ```
   Do not convert static code inspection into an unverified quantitative performance score.

---

## 10. Evidence-Based Validation Standards

1. **File Existence is NOT Validation**: Simply verifying that a file (`sitemap.xml` or `robots.txt`) exists on disk is NOT proof of SEO behavior validation.
2. **Validation Levels**:
   - **Static**: JSON schema validation, syntax checks, JSON-LD serialization tests.
   - **Framework**: Project build (`npm run build`), TypeScript typecheck (`tsc`), framework lint.
   - **Runtime**: Rendered HTML DOM inspection, HTTP header inspection.
3. **Required Reporting Structure**: Every validation check reported by the agent must specify `Command:`, `Result:`, `Evidence:`, `Impact:`. If validation could not be executed, report `Validation: NOT RUN` with a clear explanation.

---

## 11. Finding Lifecycle, Current-State Precedence & Repeatability Hardening

To ensure deterministic, audit-accurate reporting across repeated runs and test benches, the Skill enforces a strict finding lifecycle model:

### 11.1 Finding Lifecycle Statuses & Reporting Syntax

```text
Detect in Live Code
  ├── Defect exists now in code -> NEW ISSUE -> Safe fix possible?
  │     ├── Yes -> Apply fix -> Validate -> Status: NEW ISSUE → FIXED (Recorded in Files Modified)
  │     └── No  -> Blocked on input      -> Status: UNRESOLVED → USER INPUT REQUIRED (0 files modified)
  ├── Rule already satisfied in code     -> Status: ALREADY FIXED → NO CHANGE (Files Modified: 0, Recorded in Files Unchanged)
  └── Clean inspected code               -> Status: NO ISSUE (Files Modified: 0, Recorded in Files Unchanged)
```

1. **`Status: NEW ISSUE → FIXED`**: The defect genuinely existed in the current source code during the audit run and was successfully fixed and validated during the current run. The Skill must explicitly report `NEW ISSUE → FIXED` (never only `FIXED`).
2. **`Status: ALREADY FIXED → NO CHANGE`**: The audit rule was inspected, and the live codebase already satisfies the requirement prior to the current run. Action: `NO CHANGE`. Files Modified: 0. File recorded under `Files Unchanged`.
3. **`Status: UNRESOLVED → USER INPUT REQUIRED`**: A real defect or missing setting was identified, but cannot be automatically resolved safely (e.g. unknown production domain, unverified social handles). Action: `USER INPUT REQUIRED`.
4. **`Status: NO ISSUE`**: The inspected area is fully compliant with zero defects found.

### 11.2 False-Fix Prohibition
The Skill must **NEVER** report an issue as `FIXED`, `NEW ISSUE → FIXED`, `FOUND & FIXED`, or equivalent unless:
- The current run performed an actual modification to project files or configuration.
- Post-fix validation evidence confirms the issue was resolved during the current run.

If the required pattern was already in place before the run began, the Skill must report:
```text
Status: ALREADY FIXED → NO CHANGE
Files Modified: 0
Validation: PASS
```

### 11.3 Current-State Precedence Over Historical Tracker
- **Live Code Is Authoritative**: Historical entries in `seo-tracker.json` are historical logs, not source of truth for the active codebase state.
- **Reverted Baselines**: If a customer project is intentionally reverted to an earlier broken baseline between test runs, the defect genuinely exists again. The Skill must re-detect it as `NEW ISSUE → FIXED`. Historical tracker notes do not bypass live defect detection.
- **Repeatability on Already-Fixed Code**: If the code is already compliant, the Skill reports `Status: ALREADY FIXED → NO CHANGE`, regardless of past tracker history.

### 11.4 Tracking Current-Run File Changes
Audit reports must explicitly separate file interactions into three categories:
- **`Files Inspected`**: All project files analyzed during the audit.
- **`Files Modified`**: Only files actually written or modified during the current run.
- **`Files Unchanged`**: Inspected files where no modification was needed.

A file must **NEVER** be reported as modified merely because the Skill inspected or validated it.

