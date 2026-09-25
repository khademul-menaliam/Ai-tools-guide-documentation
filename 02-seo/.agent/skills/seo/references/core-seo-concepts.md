# Core SEO Concepts & Principles

This reference details universal SEO standards applicable across all web applications regardless of framework.

---

## 1. Route Classification & Sitemap Eligibility

Before performing SEO audits or generating sitemaps, every route must undergo formal classification following the **"Discover first. Classify second. Audit third."** principle:

### 1.1 Core Classification Principles
1. **URL Path Patterns are Signals Only**: Path patterns like `/v1/*`, `/api/*`, `/graphql`, `/admin/*`, `/dashboard/*` are indicative signals, NOT absolute proof. A route like `/v1/products` must NOT be assumed to be an API endpoint if the code actually renders an HTML document. Always verify actual implementation evidence (controller/handler response, view rendering, middleware).
2. **Public/Private vs `robots.txt` Separation**: Public/Private status depends on authentication and authorization middleware (session checks, role guards, login redirects), NOT `robots.txt` directives. A public page may be disallowed in `robots.txt` for technical indexing reasons (e.g. search query endpoints), but remains a public page. Conversely, a private page (`/dashboard`) remains private regardless of `robots.txt` entries.
3. **Dynamic Route Templates vs Concrete URLs**: A dynamic route pattern (`/products/{slug}`) represents a page route template (`isSeoPageCandidate: true`). The agent must NOT invent fake concrete URLs (`/products/test-product`) in the sitemap or tracker without inspecting actual content/database data sources. Do NOT reclassify or remove a dynamic page route template as an error route merely because invalid parameter values in hypothetical URLs return a 404 response.

### 1.2 Classification Criteria
1. **Public Document Pages (`isSeoPageCandidate: true`)**:
   - Must actually render or serve an HTML/document page intended for users and search crawlers.
   - Must be publicly accessible without authentication.
   - Must NOT be a redirect, error handler, or static asset.
   - **Action**: Included in HTML `sitemap.xml`; audited for metadata, canonicals, OG tags, JSON-LD, headings, images, and links.
2. **REST / JSON API Endpoints (`isSeoPageCandidate: false`)**:
   - Endpoints returning JSON, XML data, or API payloads (`/api/*`, `/v1/*`, `/graphql`).
   - **Action**: **Excluded** from HTML `sitemap.xml`; **No** HTML metadata or canonical audit.
3. **API-Backed Frontend Pages**:
   - When a frontend document page (`/about`) fetches content from a backend API endpoint (`/v1/cms-pages/about-us`), `/about` is the SEO page candidate (`isSeoPageCandidate: true`), and `/v1/cms-pages/about-us` is its API dependency (`isSeoPageCandidate: false`).
4. **Admin / Private Routes**:
   - Protected by authentication or authorization middleware (`auth`, `admin`, session guards).
   - **Action**: **Excluded** from `sitemap.xml`; disallowed in `robots.txt` if private.
5. **Redirects & Error Views**:
   - HTTP 301/302 redirects and 404/500 error pages.
   - **Action**: **Excluded** from HTML `sitemap.xml`.
6. **Static Assets**:
   - `.css`, `.js`, `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg`, `.ico`, `.pdf`, `.woff`, `.woff2` files.
   - **Action**: **Excluded** from HTML `sitemap.xml`.
7. **Unknown / Ambiguous Routes**:
   - Routes where implementation evidence is insufficient to determine response type or access level.
   - **Action**: Classified as `routeType: "unknown"`, `isSeoPageCandidate: false`, ask user; NEVER guess.

### 1.3 Benchmark Regression Cases (Cases 1–9)
- **Case 1 — Public HTML Page**: `/about` -> Renders HTML document -> `isSeoPageCandidate: true` -> Included in sitemap.
- **Case 2 — JSON API Endpoint**: `/v1/cms-pages/about-us` -> Returns JSON data -> `isSeoPageCandidate: false` -> Excluded from sitemap.
- **Case 3 — API-Backed Frontend Page**: `/about` (frontend HTML page) fetches `/v1/cms-pages/about-us` (backend JSON API). `/about` is `isSeoPageCandidate: true`; `/v1/cms-pages/about-us` is `isSeoPageCandidate: false`.
- **Case 4 — Private/Authenticated Page**: `/dashboard` -> Requires login session/guard -> `isPublic: false`, `isSeoPageCandidate: false` -> Excluded.
- **Case 5 — Redirect Route**: `/old-about` -> Returns HTTP 301/302 redirect to `/about` -> `isRedirect: true`, `isSeoPageCandidate: false` -> Excluded.
- **Case 6 — Error / 404 Handler**: `/not-found` -> Serves 404 page template -> `isError: true`, `isSeoPageCandidate: false` -> Excluded.
- **Case 7 — Static Asset**: `/app.js` -> Static JavaScript asset -> `isStaticAsset: true`, `isSeoPageCandidate: false` -> Excluded.
- **Case 8 — Ambiguous Route**: `/something` -> Implementation details unclear -> `routeType: "unknown"`, `isSeoPageCandidate: false` -> Prompt user.
- **Case 9 — Dynamic Route Template**: `/products/{slug}` -> Dynamic page route template -> `isSeoPageCandidate: true`. Do NOT invent fake URLs (`/products/sample`).

---

## 2. Title Tags & Meta Descriptions

### Title Tags (`<title>`)
- **Length**: Keep titles between **50–60 characters** (approx. 580 pixels) to prevent truncation in SERPs.
- **Format**: `Page Primary Keyword - Secondary Keyword | Brand Name`
- **Rules**:
  - Every indexable page must have a unique title tag.
  - Front-load primary keywords near the start of the title.
  - Avoid keyword stuffing.

### Meta Descriptions
- **Length**: Keep descriptions between **140–160 characters**.
- **Rules**:
  - Include a clear call-to-action (CTA) and relevant target keywords.
  - Unique per page; do not reuse default site descriptions across subpages.

---

## 3. Canonical URLs

- **Format**: Always use absolute URLs including protocol (`https://`).
- **Trailing Slashes**: Enforce consistent URL formatting across site (`https://example.com/about` vs `https://example.com/about/`).
- **Self-Referencing Canonicals**: Every canonical page should point to its own clean, canonical URL to avoid parameter duplicate issues (`?utm_source=...`, `?ref=...`).

---

## 4. Open Graph & Social Metadata

### Minimum Required OG Tags
```html
<meta property="og:title" content="Page Title — Brand Name" />
<meta property="og:description" content="Engaging summary under 160 characters." />
<meta property="og:url" content="https://example.com/page-path" />
<meta property="og:image" content="https://example.com/images/og-cover.png" />
<meta property="og:type" content="website" />
<meta property="og:site_name" content="Brand Name" />
```

### Twitter Card Tags
```html
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="Page Title — Brand Name" />
<meta name="twitter:description" content="Engaging summary under 160 characters." />
<meta name="twitter:image" content="https://example.com/images/og-cover.png" />
<meta name="twitter:site" content="@brandhandle" />
```

### Image Specifications
- **Dimensions**: **1200 x 630 pixels** (1.91:1 aspect ratio) for standard large image cards.
- **Format**: PNG, JPG, or WebP under 5 MB.
- **Path**: Always use absolute URLs (`https://...`).

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

- **Ask Before Guessing**: Never invent company registration details, phone numbers, target keywords, or social media handles.
- **TODO Marking**: If automated execution requires proceeding without user input, populate placeholders with clear comment flags:

```html
<!-- TODO (SEO): Update meta description with verified target keyword strategy -->
<meta name="description" content="TODO: Add product description for Service X" />
```
