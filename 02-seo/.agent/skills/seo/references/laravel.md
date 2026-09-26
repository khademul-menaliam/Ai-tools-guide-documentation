# Laravel SEO Reference Guide

Covers Laravel full-stack applications (Blade templates), Inertia.js (React/Vue bridge), and Spatie/Artesaos SEO packages.

---

## 1. Route Classification & API Detection Rules (Laravel)

Inspect Laravel routing evidence to classify every route before auditing or adding to sitemaps:

### 1.1 Classification Signals & Evidence
- **HTML Response Does NOT Imply SEO Candidate**: Rendering a Blade view or Inertia component is necessary but not sufficient for SEO candidacy. Transactional checkout/booking wizards, authentication forms, and confirmation views are NOT organic search targets.
- **URL Prefixes are Signals, Not Proof**: Prefixes such as `/api/`, `/v1/`, `/graphql/`, `/admin/`, `/auth/` are detection signals. Always inspect the actual route file (`routes/web.php` vs `routes/api.php`), controller response, form actions, and middleware.
- **Public Document Pages (`routeType: "page"`, `responseType: "html"`, `isSeoPageCandidate: true`)**:
  - Routes in `routes/web.php` returning a Blade view (`return view('pages.about')`) or Inertia page response serving public content/informational pages (`/`, `/about`, `/events`, `/events/{id}`, `/privacy`) without authentication middleware.
  - *Sitemap Policy*: Included in HTML `sitemap.xml` (deferred if production domain is UNRESOLVED).
- **Transactional Routes (`routeType: "transactional"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Routes handling booking wizards, checkout flows, payment processing, or cart operations.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`. Excluded from page SEO audits.
- **Authentication Routes (`routeType: "auth"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Routes rendering login, registration, password reset, or OTP verification forms.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`. Excluded from page SEO audits.
- **Post-Action & Utility Views (`routeType: "utility"`, `responseType: "html"`, `isSeoPageCandidate: false`)**:
  - Routes rendering confirmation, success, or transaction status views (`/events/{id}/confirmed`).
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`. Excluded from page SEO audits.
- **REST / JSON API Endpoints (`routeType: "api"`, `isSeoPageCandidate: false`)**:
  - Confirmed when controllers/handlers return `response()->json()`, `Illuminate\Http\JsonResponse`, `AnonymousResourceCollection`, or `JsonResource`.
  - *CMS API Nuance*: API endpoints returning JSON data (even structured CMS payloads with title/description/body markup) MUST NOT be included in HTML sitemaps or audited for HTML meta tags.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`.
  - *Audit Policy*: **No** HTML `<title>`, `<meta>`, canonical, or OG audit.
- **Admin / Private Routes (`routeType: "admin"`, `isSeoPageCandidate: false`)**:
  - Confirmed when routes use `auth`, `admin`, `verified`, `can:`, or `permission:` middleware or require session authentication.
  - *Sitemap Policy*: **Must NOT** be included in HTML `sitemap.xml`.
  - *Robots Policy*: Disallowed in `robots.txt` if private.
- **Redirect Routes (`routeType: "redirect"`, `isSeoPageCandidate: false`)**:
  - Controller actions returning `redirect()`, `redirect()->route()`, or `RedirectResponse`.
- **Static Assets (`routeType: "asset"`, `isSeoPageCandidate: false`)**:
  - Static files served from `public/` (`.css`, `.js`, `.jpg`, `.jpeg`, `.png`, `.webp`, `.svg`, `.ico`, `.pdf`, `.woff`, `.woff2`). Excluded from HTML `sitemap.xml`.

### 1.2 API-Backed Frontend Pages
If an application consumes a JSON endpoint (e.g. `/v1/cms-pages/about-us`) to render a public Blade or Inertia view (e.g. `/about`):
- `/about` is classified as `routeType: "page"`, `isSeoPageCandidate: true`.
- `/v1/cms-pages/about-us` is classified as `routeType: "api"`, `isSeoPageCandidate: false`.

### 1.3 Rendering Strategy Detection
- **`SSR`**: Blade templates rendered on server per request.
- **`Hybrid`**: Inertia.js application (SSR node server or client hydration).
- **`CSR`**: Decoupled API-only backend (serving REST JSON to separate frontend).

### 1.4 Route Hashing Strategy
- **Blade Views**: Hash path and content of `resources/views/<route>.blade.php` + shared layout `resources/views/layouts/app.blade.php`.
- **Inertia Pages**: Hash path and content of `resources/js/Pages/<Route>.vue|tsx`.

---

## 2. Blade Template SEO (`resources/views/`)

### 2.1 Reusable SEO Partial (`resources/views/partials/seo.blade.php`)

```html
<!-- resources/views/partials/seo.blade.php -->
<title>{{ $title ?? config('app.name', 'Laravel App') }}</title>
<meta name="description" content="{{ $description ?? 'Default site description.' }}">
<link rel="canonical" href="{{ $canonical ?? url()->current() }}">

<!-- Open Graph -->
<meta property="og:title" content="{{ $title ?? config('app.name') }}">
<meta property="og:description" content="{{ $description ?? 'Default site description.' }}">
<meta property="og:url" content="{{ $canonical ?? url()->current() }}">
<meta property="og:type" content="{{ $ogType ?? 'website' }}">
@if(!empty($ogImage))
<meta property="og:image" content="{{ asset($ogImage) }}">
@endif

<!-- Twitter -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="{{ $title ?? config('app.name') }}">
<meta name="twitter:description" content="{{ $description ?? 'Default site description.' }}">
@if(!empty($ogImage))
<meta name="twitter:image" content="{{ asset($ogImage) }}">
@endif

<!-- JSON-LD -->
@if(!empty($jsonLd))
<script type="application/ld+json">
    {!! str_replace('<', '\u003c', json_encode($jsonLd, JSON_UNESCAPED_SLASHES | JSON_PRETTY_PRINT)) !!}
</script>
@endif
```

### 2.2 Including in Layout / View

```html
<!-- resources/views/about.blade.php -->
@extends('layouts.app')

@section('content')
    @include('partials.seo', [
        'title' => 'About Us — Company Name',
        'description' => 'Learn about our journey and leadership team.',
        'canonical' => route('about'),
        'ogImage' => 'images/og-about.png',
        'jsonLd' => [
            '@context' => 'https://schema.org',
            '@type' => 'Organization',
            'name' => config('app.name'),
            'url' => config('app.url')
        ]
    ])

    <main>
        <h1>About Our Company</h1>
    </main>
@endsection
```

---

## 3. Inertia.js Applications (Vue or React Frontends)

When using Laravel with Inertia.js, render SEO tags dynamically using Inertia's `<Head>` component.

### Vue 3 + Inertia Example
```vue
<!-- resources/js/Pages/About.vue -->
<script setup>
import { Head } from '@inertiajs/vue3'

defineProps({
  post: Object
})
</script>

<template>
  <Head>
    <title>About Us — Enterprise Platform</title>
    <meta name="description" content="Discover our enterprise software solutions." />
    <link rel="canonical" :href="route('about')" />
    <meta property="og:title" content="About Us — Enterprise Platform" />
  </Head>

  <main>
    <h1>About Us</h1>
  </main>
</template>
```

---

## 4. Package Integration: `spatie/laravel-sitemap`

Use `spatie/laravel-sitemap` to generate dynamic XML sitemaps automatically, ensuring API and admin routes are excluded:

### Artisan Command Example (`app/Console/Commands/GenerateSitemap.php`)

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;
use Spatie\Sitemap\SitemapGenerator;
use Spatie\Sitemap\Tags\Url;

class GenerateSitemap extends Command
{
    protected $signature = 'sitemap:generate';
    protected $description = 'Generate the XML sitemap for public HTML pages only';

    public function handle()
    {
        SitemapGenerator::create(config('app.url'))
            ->hasCrawled(function (Url $url) {
                $path = parse_url($url->url, PHP_URL_PATH) ?? '/';
                // Exclude API, admin, and authentication endpoints based on precise path matching
                if (preg_match('#^/(admin|api/|v1/|login|register|dashboard)#', $path)) {
                    return null;
                }
                return $url;
            })
            ->writeToFile(public_path('sitemap.xml'));

        $this->info('Public HTML sitemap generated successfully.');
    }
}
```

---

## 5. `robots.txt` in Laravel

Place `robots.txt` directly inside the `public/` folder (`public/robots.txt`):

```text
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/

Sitemap: https://example.com/sitemap.xml
```
