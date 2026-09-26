# React & Next.js SEO Reference Guide

Covers Next.js App Router, Next.js Pages Router, and Vite + React / SPA architectures.

---

## 1. Route Classification & API Detection Rules (Next.js / React)

Inspect Next.js and React codebase evidence to classify routes before auditing or generating sitemaps:

### 1.1 Route Classification Signals & Evidence
- **HTML Response Does NOT Imply SEO Candidate**: Rendering an HTML page component (`page.tsx|jsx`) is necessary but not sufficient for SEO candidacy. Transactional flows, auth pages, and utility views render HTML but are NOT organic search targets.
- **URL Path Patterns are Signals, Not Proof**: Path prefixes (`/api/`, `/v1/`, `/admin/`, `/auth/`, `/checkout/`) are indicative detection signals, NOT absolute proof. Always inspect route file types (`page.tsx` vs `route.ts`), implementation forms, server actions, controller/handler responses, and middleware.
- **Public HTML Content Pages (`routeType: "page"`, `responseType: "html"`, `isSeoPageCandidate: true`)**:
  - App Router: `app/<route>/page.tsx|jsx` serving public informational/content/marketing pages (e.g. `/`, `/about`, `/events`, `/events/[id]`, `/privacy`).
  - Pages Router: `pages/<route>.tsx|jsx` (excluding `pages/api/*`, `_app.tsx`, `_document.tsx`).
  - Vite + React SPA: Public route view components linked in router configuration.
  - *Sitemap Policy*: Included in `sitemap.xml` (deferred if production domain is UNRESOLVED).
- **Transactional / Conversion Flow Routes (`routeType: "transactional"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - App/Pages Router pages handling booking, checkout, payment, cart actions, or order completion (e.g. `app/events/[id]/book/page.jsx`, `app/checkout/page.tsx`).
  - *Sitemap Policy*: **Must NOT** be included in `sitemap.xml`. Excluded from page SEO audits.
- **Authentication & Account Access Routes (`routeType: "auth"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - App/Pages Router pages handling login, registration, password recovery, or OTP verification (e.g. `app/auth/login/page.tsx`, `app/auth/verify-otp/page.tsx`).
  - *Sitemap Policy*: **Must NOT** be included in `sitemap.xml`. Excluded from page SEO audits.
- **Post-Action & Utility Views (`routeType: "utility"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Confirmation screens, success notifications, cancellation notices (e.g. `app/events/[id]/confirmed/page.jsx`).
  - *Sitemap Policy*: **Must NOT** be included in `sitemap.xml`. Excluded from page SEO audits.
- **REST / JSON API Endpoints (`routeType: "api"`, `isSeoPageCandidate: false`)**:
  - App Router Route Handlers: `app/api/<endpoint>/route.ts|js` or `app/<endpoint>/route.ts|js` returning `NextResponse.json()` or `Response.json()`.
  - Pages Router API Routes: `pages/api/<endpoint>.ts|js`.
  - *CMS API Nuance*: API endpoints returning JSON data (even structured CMS payloads with title/description/body markup) MUST NOT be included in HTML sitemaps or audited for HTML meta tags.
  - *Sitemap Policy*: **Must NOT** be included in `sitemap.xml`.
  - *Audit Policy*: **No** HTML `<title>`, `<meta>`, canonical, or OG audit.
- **Admin / Private Routes (`routeType: "admin"`, `isSeoPageCandidate: false`)**:
  - Confirmed when routes are protected by `middleware.ts` authentication checks, session cookies, or auth guards, NOT `robots.txt` rules alone.
  - *Sitemap Policy*: **Must NOT** be included in `sitemap.xml`.
- **Redirect Routes (`routeType: "redirect"`, `isSeoPageCandidate: false`)**:
  - Routes declaring `redirect()` in Server Components or configured in `next.config.js` `redirects()`.
- **Error / Not-Found Pages (`routeType: "error"`, `isSeoPageCandidate: false`)**:
  - App Router `not-found.tsx`, `error.tsx`, or Pages Router `404.tsx`, `500.tsx`.
- **Static Assets (`routeType: "asset"`, `isSeoPageCandidate: false`)**:
  - Static files (`.css`, `.js`, `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg`, `.ico`, `.pdf`, `.woff`, `.woff2`). Excluded from `sitemap.xml`.

### 1.2 Rendering Strategy Detection
- **`'use client'` Nuance**: The `'use client'` directive marks an individual Client Component boundary; it does **NOT** mean the entire page or project is `CSR`.
- **`generateStaticParams` Nuance**: `generateStaticParams` indicates static param generation for specific dynamic route segments; it does **NOT** prove the entire project is `SSG`.
- **`SSG`**: All audited routes use static generation (`generateStaticParams`, static pages, or `getStaticProps`).
- **`SSR`**: Routes rely on dynamic server components (`headers()`, `cookies()`, `noStore()`) or `getServerSideProps`.
- **`CSR`**: Pure React SPA (Vite + React, CRA) or Next.js app with `output: 'export'` where all routes execute strictly on the client.
- **`Hybrid`**: Application mixes static pages (`SSG`) and server-rendered routes (`SSR`).
- **`Unknown`**: Set when available rendering evidence is insufficient.

### 1.3 Route Source Hashing & Shared File Invalidation
- **Route-Local Files**: Combine relative path and contents of `app/<route>/page.tsx|jsx` or `pages/<route>.tsx|jsx`.
- **Shared SEO Files**: Include shared layouts (`app/layout.tsx|jsx`, `pages/_app.tsx|jsx`) in `globalSeoHash`. Modifications to `app/layout.tsx` invalidate dependent route hashes.

---

## 2. Next.js App Router (`app/` directory)

### 2.1 Static Metadata
Export a static `metadata` object from `page.tsx` or `layout.tsx`.

```tsx
// app/about/page.tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "About Us — Brand Name",
  description: "Learn about our company mission and engineering team.",
  alternates: {
    canonical: "https://example.com/about",
  },
  openGraph: {
    title: "About Us — Brand Name",
    description: "Learn about our company mission and engineering team.",
    url: "https://example.com/about",
    siteName: "Brand Name",
    images: [{ url: "https://example.com/og-about.png", width: 1200, height: 630 }],
    locale: "en_US",
    type: "website",
  },
  twitter: {
    card: "summary_large_image",
    title: "About Us — Brand Name",
    description: "Learn about our company mission and engineering team.",
    images: ["https://example.com/og-about.png"],
  },
};
```

### 2.2 Dynamic Metadata (`generateMetadata`) & Next.js 15+ Async `params` Rules
Use when page content is fetched dynamically from an API or database.

#### Next.js 15+ Pattern (Async `params`)
In Next.js 15+, `params` passed to `generateMetadata` (and page/layout components) is a `Promise` and **MUST** be awaited before property access.

```tsx
// app/products/[slug]/page.tsx
import type { Metadata } from "next";

type Props = {
  params: Promise<{ slug: string }>; // Next.js 15+ typed as Promise
};

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const { slug } = await params; // MUST await params before accessing slug
  const product = await fetchProduct(slug);

  return {
    title: `${product.name} | Store Name`,
    description: product.summary,
    alternates: {
      canonical: `https://example.com/products/${slug}`,
    },
    openGraph: {
      title: product.name,
      description: product.summary,
      images: [{ url: product.image, width: 1200, height: 630 }],
    },
  };
}
```

#### Required Audit & Remediation Workflow for `generateMetadata`:
1. **Detect Next.js Version**: Inspect `package.json` `dependencies` / `devDependencies` for `next` version (e.g., `^15.0.0`, `15.x`).
2. **Inspect `params` Access in Live Code**: Check if `generateMetadata({ params })` accesses `params.id`, `params.slug`, or other properties directly without `await params`.
3. **Classify Finding Lifecycle**:
   - **`NEW ISSUE`**: Direct unawaited property access found in live code. Framework runtime defect present. Proceed to step 4.
   - **`ALREADY FIXED`**: `await params` is already implemented. Report `Status: ALREADY FIXED → NO CHANGE`, `Files Modified: 0`, `Validation: PASS`. 0 files modified; record in `Files Unchanged`. NEVER claim "FOUND & FIXED" or only "FIXED".
4. **Automated Fix**: Update the signature and function body to await `params` (`const { slug } = await params;` and `params: Promise<...>`) when the change is unambiguous.
5. **Validate & Report**: Execute typecheck/build/syntax validation. On success, report `Status: NEW ISSUE → FIXED`, `Action: Modified <path>`, `Validation: PASS`, and add to `Files Modified`.
6. **Indeterminate Version**: If Next.js version cannot be verified, do not guess; prompt user or mark `Status: UNRESOLVED → USER INPUT REQUIRED`.

> **Version Summary**:
> - **Next.js 15+**: `params` is a `Promise<{ slug: string }>` -> `const { slug } = await params;`
> - **Next.js 14 and earlier**: `params` is synchronous -> `const { slug } = params;`

### 2.3 Hierarchical Metadata Inheritance Resolution (Next.js App Router)

Next.js App Router evaluates metadata in a hierarchical chain starting from the root layout down to the page component:
```text
app/layout.tsx (Root Layout)
  └── app/(segment)/layout.tsx (Nested Layout)
        └── app/(segment)/[id]/page.tsx (Page Component)
```

The Skill must evaluate `title` and `description` independently using separate resolution calls (`resolveMetadataField(route, "title")` and `resolveMetadataField(route, "description")`):

#### 2.3.1 Resolution Hierarchy Rules
1. **Page-Level Verification**:
   - Inspect whether the page component (`page.tsx|jsx`) exports a static `metadata` object or `export async function generateMetadata()`.
   - Inspect the actual properties defined/returned in AST/exports:
     - If `title` is explicitly declared in `metadata` or returned by `generateMetadata()` -> `metadataSource.title = "page"`.
     - If `description` is explicitly declared in `metadata` or returned by `generateMetadata()` -> `metadataSource.description = "page"`.
   - Page-level declarations always take precedence over all parent layouts.
2. **Parent Layout Traversal (When Field is Absent at Page Level)**:
   - If a field is NOT declared at page level, traverse upward through the route's applicable layout hierarchy:
     - Check nearest nested layout (e.g. `app/(segment)/layout.tsx`) for exported `metadata` or `generateMetadata()`.
     - If found, that nearest nested layout provides the field (`metadataSource.<field> = "inherited"`).
     - Otherwise, continue up the layout tree to grandparent layouts and finally root layout (`app/layout.tsx`).
   - If an applicable parent layout defines the field -> `metadataSource.<field> = "inherited"`.
3. **Missing Classification**:
   - If neither the page nor any applicable parent layout in the route's chain defines the field -> `metadataSource.<field> = "missing"`.

#### 2.3.2 Strict Prohibitions & Anti-Patterns
- **No Naive "Absent = Inherited"**: Do NOT infer `inherited` merely because a field is absent from the page file. You MUST inspect parent layouts and verify that an applicable layout actually exports `metadata.<field>` or `generateMetadata()`.
- **No "generateMetadata = Both Page"**: NEVER infer `title: "page"` and `description: "page"` merely because `generateMetadata()` exists. Inspect the function's return object. If it only returns `{ title: "..." }`, then `description` must be resolved against parent layouts (`inherited` or `missing`).
- **No Naive Substring Matching**: Prohibit naive regex/string searches (`code.includes('title:')`, `code.includes('description:')`, `code.includes('generateMetadata')`). These cause false positives with component props (`<Card title="..." />`), data payloads, or comments. Inspect exported metadata AST structures.

#### 2.3.3 Inheritance & Dynamic Return Scenarios
- **Scenario 1 (Page Title + Root Description)**:
  - `app/layout.jsx` exports `metadata = { title: "Brand", description: "Global description" }`
  - `app/(main)/about/page.jsx` exports `metadata = { title: "About Us" }`
  - Result: `metadataSource: { title: "page", description: "inherited" }` (Evidence: Title in `page.jsx`, Description from `app/layout.jsx`).
- **Scenario 2 (Nested Layout Precedence)**:
  - `app/layout.jsx` exports `metadata = { title: "Brand", description: "Global" }`
  - `app/(events)/layout.jsx` exports `metadata = { description: "Explore upcoming events" }`
  - `app/(events)/events/[id]/page.jsx` exports `generateMetadata()` returning `{ title: "Event Name" }`
  - Result: `metadataSource: { title: "page", description: "inherited" }` (Evidence: Title in `page.jsx`, Description from nearest parent `app/(events)/layout.jsx`).
- **Scenario 3 (Page Overriding Nested Layout)**:
  - `app/(events)/layout.jsx` exports `metadata = { title: "All Events" }`
  - `app/(events)/events/[id]/page.jsx` exports `generateMetadata()` returning `{ title: "Rock Concert", description: "Live concert" }`
  - Result: `metadataSource: { title: "page", description: "page" }` (Evidence: Page `generateMetadata()` overrides layout title).
- **Scenario 4 (Truly Missing Field)**:
  - `app/(main)/terms/page.jsx` exports `metadata = { title: "Terms" }`
  - Neither `app/(main)/layout.jsx` nor `app/layout.jsx` exports `description`
  - Result: `metadataSource: { title: "page", description: "missing" }` (Evidence: Title in `page.jsx`, no layout in hierarchy defines description).

#### 2.3.4 Validation Reporting Format
Validation reports should format concrete field-level evidence:
```text
Route: /about
Title source: page
Description source: inherited
Evidence:
  page.jsx → exports metadata.title ("About Us")
  page.jsx → does not define description
  nearest applicable layout (app/(main)/layout.jsx) → exports metadata.description ("Company Overview")
```

### 2.4 App Router `sitemap.ts` and `robots.ts`

> **Canonical Domain Rule**: If the production website domain is `UNRESOLVED`, **DO NOT** generate a concrete `sitemap.ts` / `sitemap.xml` with hardcoded fallback domains (`https://example.com`, `http://localhost`, or backend API URLs). Record `globalStatus.sitemap: "pending_domain"` and defer file creation until domain is confirmed.

When domain is verified, exclude API routes (`app/api/*`) from `sitemap.ts`:

```ts
// app/sitemap.ts
import { MetadataRoute } from "next";

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const baseUrl = process.env.NEXT_PUBLIC_SITE_URL || "https://mybrand.com";
  const routes = ["", "/about", "/services"].map((route) => ({
    url: `${baseUrl}${route}`,
    lastModified: new Date(),
    changeFrequency: "weekly" as const,
    priority: route === "" ? 1.0 : 0.8,
  }));

  return routes;
}
```

```ts
// app/robots.ts
import { MetadataRoute } from "next";

export default function robots(): MetadataRoute.Robots {
  const baseUrl = process.env.NEXT_PUBLIC_SITE_URL || "https://mybrand.com";
  return {
    rules: [
      {
        userAgent: "*",
        allow: "/",
        disallow: ["/api/", "/admin/"],
      },
    ],
    sitemap: `${baseUrl}/sitemap.xml`,
  };
}
```

### 2.4 JSON-LD in App Router
Injected in page components as a `<script>` block with XSS replacement:

```tsx
// app/page.tsx
export default function HomePage() {
  const jsonLd = {
    "@context": "https://schema.org",
    "@type": "WebSite",
    name: "Brand Name",
    url: "https://example.com",
  };

  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{
          __html: JSON.stringify(jsonLd).replace(/</g, "\\u003c"),
        }}
      />
      <main><h1>Welcome to Brand Name</h1></main>
    </>
  );
}
```

---

## 3. Next.js Pages Router (`pages/` directory)

In Pages Router, use `next/head` inside pages or a reusable `SEO` wrapper component. Exclude `pages/api/*` endpoints from SEO auditing.

```tsx
// components/SEO.tsx
import Head from "next/head";

type SEOProps = {
  title: string;
  description: string;
  canonical?: string;
  ogImage?: string;
};

export const SEO = ({ title, description, canonical, ogImage }: SEOProps) => (
  <Head>
    <title>{title}</title>
    <meta name="description" content={description} />
    {canonical && <link rel="canonical" href={canonical} />}
    <meta property="og:title" content={title} />
    <meta property="og:description" content={description} />
    {ogImage && <meta property="og:image" content={ogImage} />}
    <meta name="twitter:card" content="summary_large_image" />
  </Head>
);
```

---

## 4. Vite + React / React SPA

For Vite + React SPAs, manage metadata using `react-helmet-async`.

### Reusable Helmet Component
```tsx
// src/components/SEO.tsx
import { Helmet } from "react-helmet-async";

interface SEOProps {
  title: string;
  description: string;
  canonical?: string;
  ogImage?: string;
  jsonLd?: Record<string, unknown>;
}

export function SEO({ title, description, canonical, ogImage, jsonLd }: SEOProps) {
  const siteUrl = "https://example.com";
  const fullCanonical = canonical || siteUrl;

  return (
    <Helmet>
      <title>{title}</title>
      <meta name="description" content={description} />
      <link rel="canonical" href={fullCanonical} />

      <meta property="og:title" content={title} />
      <meta property="og:description" content={description} />
      <meta property="og:url" content={fullCanonical} />
      {ogImage && <meta property="og:image" content={ogImage} />}

      <meta name="twitter:card" content="summary_large_image" />
      
      {jsonLd && (
        <script type="application/ld+json">
          {JSON.stringify(jsonLd).replace(/</g, "\\u003c")}
        </script>
      )}
    </Helmet>
  );
}
```
