# Angular SEO Reference Guide

Covers Angular (v16+) applications using Angular's native `Title` and `Meta` services, SSR (Server-Side Rendering), and structured data injection.

---

## 1. Route Classification & API Detection Rules (Angular)

Inspect Angular application evidence to classify routes before auditing or generating sitemaps:

### 1.1 Classification Signals & Evidence
- **HTML Response Does NOT Imply SEO Candidate**: Rendering an Angular component template is necessary but not sufficient for SEO candidacy. Transactional booking/checkout steps, auth forms, and status utility views are NOT organic search targets.
- **URL Path Patterns are Signals, Not Proof**: Route path strings (`/admin`, `/api`, `/auth`, `/checkout`) are detection signals. Inspect `Routes` config, component render targets, forms, `HttpClient` service usages, and route guards.
- **Public Component Document Routes (`routeType: "page"`, `responseType: "html"`, `isSeoPageCandidate: true`)**:
  - Routes declared in Angular `Routes` module/config that render public content/informational page components without authentication guards.
  - *Sitemap Policy*: Included in HTML `sitemap.xml` (deferred if production domain is UNRESOLVED).
- **Transactional Routes (`routeType: "transactional"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Angular routes handling booking flows, checkout steps, payment processing, or cart operations.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`. Excluded from page SEO audits.
- **Authentication Routes (`routeType: "auth"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Routes rendering login, registration, password recovery, or OTP verification components.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`. Excluded from page SEO audits.
- **Post-Action & Utility Views (`routeType: "utility"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Routes rendering confirmation, success, or transaction status components.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`. Excluded from page SEO audits.
- **API Services / Data Endpoints (`routeType: "api"`, `isSeoPageCandidate: false`)**:
  - Angular `HttpClient` services (`@Injectable`) fetching backend JSON data, or SSR backend endpoints returning API responses.
  - *CMS API Nuance*: API endpoints returning JSON data (even structured CMS payloads with title/description/body markup) MUST NOT be included in HTML sitemaps or audited for HTML meta tags.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`.
  - *Audit Policy*: **No** HTML `<title>`, `<meta>`, canonical, or OG audit.
- **Admin / Protected Routes (`routeType: "admin"`, `isSeoPageCandidate: false`)**:
  - Confirmed when routes are protected by `canActivate`, `canActivateChild`, or `canMatch` authentication guards, NOT `robots.txt` rules alone.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`.
- **Redirect Routes (`routeType: "redirect"`, `isSeoPageCandidate: false`)**:
  - Routes declaring `redirectTo: '/target-path'` in Angular `Routes` config.
- **Static Assets (`routeType: "asset"`, `isSeoPageCandidate: false`)**:
  - Static assets listed in `angular.json` or served from `assets/` (`.css`, `.js`, `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg`, `.ico`, `.pdf`, `.woff`, `.woff2`). Excluded from HTML `sitemap.xml`.

### 1.2 Rendering Strategy Detection
- **`SSR`**: Contains `@angular/ssr`, hydration setup (`provideClientHydration()`), or `server.ts` entry point.
- **`SSG`**: Angular CLI prerendering enabled in `angular.json` (`prerender: true`).
- **`CSR`**: Standard browser-only Angular SPA without SSR/prerender engine.
- **`Unknown`**: Set when SSR setup cannot be verified with certainty.

### 1.3 Route Source Hashing Strategy
- Combine relative path and contents of component `.ts` file handling the route (e.g. `src/app/pages/about/about.component.ts`).

---

## 2. Angular Title & Meta Services

Angular provides built-in services for manipulating document head tags dynamically within component lifecycle hooks.

### Reusable `SeoService` Implementation

```typescript
// src/app/services/seo.service.ts
import { Injectable, inject } from '@angular/core';
import { Title, Meta } from '@angular/platform-browser';
import { DOCUMENT } from '@angular/common';

export interface SeoData {
  title: string;
  description: string;
  canonicalUrl?: string;
  ogImage?: string;
  jsonLd?: Record<string, unknown>;
}

@Injectable({
  providedIn: 'root'
})
export class SeoService {
  private titleService = inject(Title);
  private metaService = inject(Meta);
  private document = inject(DOCUMENT);

  updateSeoData(data: SeoData): void {
    // 1. Title & Description
    this.titleService.setTitle(data.title);
    this.metaService.updateTag({ name: 'description', content: data.description });

    // 2. Open Graph & Twitter
    this.metaService.updateTag({ property: 'og:title', content: data.title });
    this.metaService.updateTag({ property: 'og:description', content: data.description });
    if (data.ogImage) {
      this.metaService.updateTag({ property: 'og:image', content: data.ogImage });
      this.metaService.updateTag({ name: 'twitter:image', content: data.ogImage });
    }
    this.metaService.updateTag({ name: 'twitter:card', content: 'summary_large_image' });

    // 3. Canonical Tag
    if (data.canonicalUrl) {
      this.setCanonicalUrl(data.canonicalUrl);
    }

    // 4. JSON-LD Injection
    if (data.jsonLd) {
      this.injectJsonLd(data.jsonLd);
    }
  }

  private setCanonicalUrl(url: string): void {
    let link: HTMLLinkElement | null = this.document.querySelector('link[rel="canonical"]');
    if (!link) {
      link = this.document.createElement('link');
      link.setAttribute('rel', 'canonical');
      this.document.head.appendChild(link);
    }
    link.setAttribute('href', url);
  }

  private injectJsonLd(schema: Record<string, unknown>): void {
    let script: HTMLScriptElement | null = this.document.querySelector('script[type="application/ld+json"]');
    if (!script) {
      script = this.document.createElement('script');
      script.setAttribute('type', 'application/ld+json');
      this.document.head.appendChild(script);
    }
    script.text = JSON.stringify(schema).replace(/</g, '\\u003c');
  }
}
```

---

## 3. Usage in Angular Components

```typescript
// src/app/pages/about/about.component.ts
import { Component, OnInit, inject } from '@angular/core';
import { SeoService } from '../../services/seo.service';

@Component({
  selector: 'app-about',
  standalone: true,
  template: `
    <main>
      <h1>About Our Platform</h1>
    </main>
  `
})
export class AboutComponent implements OnInit {
  private seo = inject(SeoService);

  ngOnInit(): void {
    this.seo.updateSeoData({
      title: 'About Us — Enterprise Platform',
      description: 'Learn about our engineering team and mission.',
      canonicalUrl: 'https://example.com/about',
      ogImage: 'https://example.com/assets/og-about.png',
      jsonLd: {
        '@context': 'https://schema.org',
        '@type': 'Organization',
        name: 'Enterprise Platform',
        url: 'https://example.com'
      }
    });
  }
}
```

---

## 4. Server-Side Rendering (Angular SSR)

For Angular applications where crawlability is required:
- Enable Angular SSR via `@angular/ssr` (Angular 17+) or `@nguniversal/express-engine`.
- Angular SSR executes `SeoService` logic on the Node server during initial render, returning fully populated `<head>` markup directly in initial HTML responses.

---

## 5. Static Assets (`sitemap.xml` & `robots.txt`)

Place static `sitemap.xml` and `robots.txt` files inside the `src/assets/` directory or `public/` directory, and verify they are listed in `angular.json` under `assets`:

```json
"assets": [
  "src/favicon.ico",
  "src/assets",
  "src/robots.txt",
  "src/sitemap.xml"
]
```
