# React & Next.js SEO Reference Guide

Covers Next.js App Router, Next.js Pages Router, and Vite + React / SPA architectures.

---

## 1. Route Classification & API Detection Rules (Next.js / React)

Inspect Next.js and React codebase evidence to classify routes before auditing or generating sitemaps:

### 1.1 Route Classification Signals & Evidence
- **URL Path Patterns are Signals, Not Proof**: Path prefixes (`/api/`, `/v1/`, `/admin/`) are indicative detection signals, NOT absolute proof. Always inspect route file types (`page.tsx` vs `route.ts`), controller/handler responses, and middleware.
- **Public HTML Pages (`routeType: "page"`, `responseType: "html"`, `isSeoPageCandidate: true`)**:
  - App Router: `app/<route>/page.tsx|jsx`.
  - Pages Router: `pages/<route>.tsx|jsx` (excluding `pages/api/*`, `_app.tsx`, `_document.tsx`).
  - Vite + React SPA: Route view components linked in router configuration.
  - *Sitemap Policy*: Included in `sitemap.xml`.
- **REST / JSON API Endpoints (`routeType: "api"`, `isSeoPageCandidate: false`)**:
  - App Router Route Handlers: `app/api/<endpoint>/route.ts|js` or `app/<endpoint>/route.ts|js` returning `NextResponse.json()` or `Response.json()`.
  - Pages Router API Routes: `pages/api/<endpoint>.ts|js`.
  - *Sitemap Policy*: **Must NOT** be included in `sitemap.xml`.
  - *Audit Policy*: **No** HTML `<title>`, `<meta>`, canonical, or OG audit.
- **Admin / Private Routes (`routeType: "admin"`, `isSeoPageCandidate: false`)**:
  - Confirmed when routes are protected by `middleware.ts` authentication checks, session cookies, or auth guards, NOT `robots.txt` rules alone.
  - *Sitemap Policy*: **Must NOT** be included in `sitemap.xml`.
- **Redirect Routes (`routeType: "redirect"`, `isSeoPageCandidate: false`)**:
  - Routes declaring `redirect()` in Server Components or configured in `next.config.js` `redirects()`.
- **Error / Not-Found Pages (`routeType: "error"`, `isSeoPageCandidate: false`)**:
  - App Router `not-found.tsx`, `error.tsx`, or Pages Router `404.tsx`, `500.tsx`.

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

### 2.2 Dynamic Metadata (`generateMetadata`)
Use when page content is fetched dynamically from an API or database.

```tsx
// app/products/[slug]/page.tsx
import type { Metadata } from "next";

type Props = {
  params: Promise<{ slug: string }>; // Next.js 15+ typed as Promise
};

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const { slug } = await params; // Next.js 15 requires awaiting params
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

> **Version Warning**: In **Next.js 15+**, `params` is a `Promise<{ slug: string }>` and must be `await`ed. In **Next.js 14 and earlier**, `params` is a synchronous object (`{ slug: string }`).

### 2.3 App Router `sitemap.ts` and `robots.ts`

Exclude API routes (`app/api/*`) from `sitemap.ts`:

```ts
// app/sitemap.ts
import { MetadataRoute } from "next";

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const routes = ["", "/about", "/services"].map((route) => ({
    url: `https://example.com${route}`,
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
  return {
    rules: [
      {
        userAgent: "*",
        allow: "/",
        disallow: ["/api/", "/admin/"],
      },
    ],
    sitemap: "https://example.com/sitemap.xml",
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
