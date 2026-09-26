# Vue & Nuxt SEO Reference Guide

Covers Nuxt 3 SSR applications and Vue 3 SPAs (using Unhead / `@unhead/vue`).

---

## 1. Route Classification & API Detection Rules (Vue / Nuxt)

Inspect Nuxt and Vue codebase evidence to classify routes before auditing or generating sitemaps:

### 1.1 Classification Signals & Evidence
- **HTML Response Does NOT Imply SEO Candidate**: Serving an HTML/Vue template is necessary but not sufficient for SEO candidacy. Transactional checkout/booking flows, auth forms, and post-action views are NOT organic search targets.
- **URL Path Patterns are Signals, Not Proof**: Path prefixes (`/api/`, `/v1/`, `/admin/`, `/auth/`) are indicative detection signals, NOT absolute proof. Inspect file locations (`pages/*.vue` vs `server/api/*.ts`), handler responses (`defineEventHandler` returning JSON), forms, mutations, and route middleware.
- **Public HTML Content Pages (`routeType: "page"`, `responseType: "html"`, `isSeoPageCandidate: true`)**:
  - Nuxt: `pages/*.vue` or `pages/<route>/index.vue` serving public informational/content/marketing pages (`/`, `/about`, `/events`, `/events/[id]`, `/privacy`).
  - Vue SPA: Vue Router routes rendering public document components.
  - *Sitemap Policy*: Included in `@nuxtjs/sitemap` (deferred if production domain is UNRESOLVED).
- **Transactional Flow Routes (`routeType: "transactional"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Pages handling booking, checkout, payment processing, cart mutations, or step wizards (`pages/events/[id]/book.vue`, `pages/checkout/index.vue`).
  - *Sitemap Policy*: **Must NOT** be included in `@nuxtjs/sitemap`. Excluded from page SEO audits.
- **Authentication Routes (`routeType: "auth"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Pages handling login, registration, password recovery, or OTP verification (`pages/auth/login.vue`, `pages/auth/verify-otp.vue`).
  - *Sitemap Policy*: **Must NOT** be included in `@nuxtjs/sitemap`. Excluded from page SEO audits.
- **Post-Action & Utility Views (`routeType: "utility"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Confirmation screens, success notifications, cancellation notices (`pages/events/[id]/confirmed.vue`).
  - *Sitemap Policy*: **Must NOT** be included in `@nuxtjs/sitemap`. Excluded from page SEO audits.
- **REST / JSON API Endpoints (`routeType: "api"`, `isSeoPageCandidate: false`)**:
  - Nuxt Server Routes: `server/api/*.ts|js` or `server/routes/*.ts|js` returning JSON data via `defineEventHandler()`.
  - *CMS API Nuance*: API endpoints returning JSON data (even structured CMS payloads with title/description/body markup) MUST NOT be included in HTML sitemaps or audited for HTML meta tags.
  - *Sitemap Policy*: **Must NOT** be included in `@nuxtjs/sitemap`.
  - *Audit Policy*: **No** HTML `<title>`, `<meta>`, canonical, or OG audit.
- **Admin / Private Routes (`routeType: "admin"`, `isSeoPageCandidate: false`)**:
  - Routes protected by route middleware (`definePageMeta({ middleware: 'auth' })`) or auth guards, NOT `robots.txt` rules alone.
  - *Sitemap Policy*: **Must NOT** be included in `@nuxtjs/sitemap`.
- **Redirect Routes (`routeType: "redirect"`, `isSeoPageCandidate: false`)**:
  - Routes configured with `navigateTo()` or `definePageMeta({ redirect: '/new-url' })`.
- **Static Assets (`routeType: "asset"`, `isSeoPageCandidate: false`)**:
  - Static files (`.css`, `.js`, `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg`, `.ico`, `.pdf`, `.woff`, `.woff2`). Excluded from `@nuxtjs/sitemap`.

### 1.2 Rendering Strategy Detection
- **`SSR`**: Default Nuxt 3 mode (`ssr: true` in `nuxt.config.ts`).
- **`SSG`**: Nuxt prerendering (`nuxi generate` or explicit `prerender` / `routeRules`).
- **`CSR`**: Nuxt with `ssr: false` or Vue 3 SPA without server engine.
- **`Hybrid`**: Nuxt with route-specific rendering rules (`routeRules` mixing `ssr`, `prerender`, and `swr`).
- **`Unknown`**: Set when rendering configuration cannot be determined with certainty.

### 1.3 Route Source Hashing Strategy
- Combine relative path and contents of `pages/<route>.vue` + shared layout `layouts/default.vue` or `app.vue`.

---

## 2. Nuxt 3 (`useSeoMeta` & `useHead`)

Nuxt 3 includes Unhead natively. Use `useSeoMeta` for clean type-safe meta definitions.

### 2.1 Page-Level SEO with `useSeoMeta`

```vue
<!-- pages/about.vue -->
<script setup lang="ts">
useSeoMeta({
  title: 'About Us — Brand Name',
  ogTitle: 'About Us — Brand Name',
  description: 'Discover our mission, team, and company history.',
  ogDescription: 'Discover our mission, team, and company history.',
  ogImage: 'https://example.com/og-about.png',
  twitterCard: 'summary_large_image',
})

useHead({
  link: [
    { rel: 'canonical', href: 'https://example.com/about' }
  ],
  script: [
    {
      type: 'application/ld+json',
      children: JSON.stringify({
        '@context': 'https://schema.org',
        '@type': 'Organization',
        name: 'Brand Name',
        url: 'https://example.com',
      }).replace(/</g, '\\u003c')
    }
  ]
})
</script>

<template>
  <main>
    <h1>About Us</h1>
  </main>
</template>
```

### 2.2 Global Configuration (`nuxt.config.ts`)

Define site-wide defaults in `nuxt.config.ts`:

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  app: {
    head: {
      titleTemplate: '%s — Site Name',
      defaultTitle: 'Site Name',
      meta: [
        { charset: 'utf-8' },
        { name: 'viewport', content: 'width=device-width, initial-scale=1' },
        { name: 'format-detection', content: 'telephone=no' }
      ],
      link: [
        { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
      ]
    }
  },
  modules: [
    '@nuxtjs/sitemap',
    '@nuxtjs/robots'
  ],
  site: {
    url: 'https://example.com',
    name: 'Brand Name'
  }
})
```

---

## 3. Nuxt 3 Sitemap & Robots (`@nuxtjs/sitemap` & `@nuxtjs/robots`)

With official Nuxt modules, sitemaps and `robots.txt` are configured declaratively, excluding API endpoints:

### `nuxt.config.ts` Module Setup

```ts
export default defineNuxtConfig({
  modules: ['@nuxtjs/sitemap', '@nuxtjs/robots'],
  site: {
    url: 'https://example.com',
  },
  sitemap: {
    exclude: ['/admin/**', '/api/**', '/server/**'],
  },
  robots: {
    userAgent: '*',
    allow: '/',
    disallow: ['/admin', '/api'],
    sitemap: 'https://example.com/sitemap.xml',
  }
})
```

---

## 4. Vue 3 SPA (with `@unhead/vue`)

For Vue 3 single page applications, install `@unhead/vue` to manage document head states.

### Setup in `main.ts`
```ts
import { createApp } from 'vue'
import { createHead } from '@unhead/vue'
import App from './App.vue'

const app = createApp(App)
const head = createHead()

app.use(head)
app.mount('#app')
```

### Usage in Components
```vue
<script setup lang="ts">
import { useHead } from '@unhead/vue'

useHead({
  title: 'Services — Brand Name',
  meta: [
    { name: 'description', content: 'Comprehensive enterprise solutions.' },
    { property: 'og:title', content: 'Services — Brand Name' },
    { property: 'og:image', content: 'https://example.com/og-services.png' }
  ],
  link: [
    { rel: 'canonical', href: 'https://example.com/services' }
  ]
})
</script>
```
