# Technical Reference & Adaptation Guide: `react-seo-skills`

This document provides a technical analysis of the base repository [`daniel-amekpoagbe/react-seo-skills`](https://github.com/daniel-amekpoagbe/react-seo-skills) and details how its features were transformed into our portable, multi-framework SEO agent skill.

---

## 1. Base Repository Breakdown (`react-seo-skills`)

### 1.1 Architecture & Components
The base repository provides an SEO skill for Cursor, Claude Code, and Codex centered around JavaScript and TypeScript web frameworks.

```text
react-seo-skills/
├── bin/install.js                  # CLI installer for skill discovery
├── skill/
│   ├── SKILL.md                    # Core prompt, rules, detection & audit table
│   └── references/                 # Reference guides
│       ├── app-router.md           # Next.js App Router metadata & sitemaps
│       ├── pages-router.md         # Next.js Pages Router & next/head
│       ├── react-vite.md           # Vite + React & SPA patterns
│       ├── astro.md                # Astro SSG/SSR patterns
│       ├── react-helmet-async.md   # Helmet provider setup
│       ├── structured-data.md      # Schema.org JSON-LD templates & XSS rules
│       ├── keywords.md             # Keyword strategy & clustering
│       ├── geo.md                  # Generative Engine Optimization / AI search
│       ├── language.md             # JS vs TS code generation rules
│       └── validation.md           # Rich results & performance validation links
```

### 1.2 Detection & Audit Flow in Base Skill
1. **Stack Detection**: Relies on scanning `package.json` and directory structure for React/Next.js/Astro indicators.
2. **Stateless Audits**: Performs one-shot code analysis and returns Markdown reports categorizing findings into `Critical`, `Suggestion`, and `OK`.
3. **Information Prompting**: Explicitly instructs the agent to ask the developer for real business details rather than inventing placeholders.

---

## 2. Architectural Comparison

| Architectural Dimension | Base `react-seo-skills` Repository | Our Extended SEO Agent Skill (Phase 3.1.1 Hardened) |
| :--- | :--- | :--- |
| **Framework Range** | React (Next.js App/Pages, Vite), Astro | Next.js/React, Vue/Nuxt, Angular, Laravel (with extensible plugin architecture) |
| **Route Classification** | Basic / Assumptions | Formal Classification (`page`, `api`, `admin`, `redirect`, `error`, `asset`, `unknown`) |
| **API Endpoint Handling** | None | REST/JSON API endpoints explicitly excluded from HTML sitemaps and meta audits |
| **State Persistence** | None (stateless one-shot audit) | `system-docs/seo-config.json` & `system-docs/seo-tracker.json` (`schemaVersion: "1.0"`) |
| **Audit Strategy** | Re-audits entire codebase on every invocation | Incremental audit (audits only `NEW`, `MODIFIED`, or incomplete `isSeoPageCandidate` routes) |
| **Change Detection** | Timestamp / None | Deterministic SHA-256 content hashing (`contentHash: "sha256:..."`) |
| **Shared File Invalidation**| None | Shared root layouts (`app/layout.tsx`) invalidate dependent route hashes (`globalSeoHash`) |
| **Legacy Tracker Migration** | None | Backfills missing `contentHash` & classification entries automatically |
| **Deleted Route Handling**| None | Automatic discovery vs tracker comparison; cleans up deleted routes |
| **Rendering Detection** | Basic / Implied | Conservative classification (`SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown`) |
| **Skill Location** | Installed via global CLI tool | Self-contained inside project `.agent/skills/seo/` for high portability |
| **Deployment Guidance** | Static checklist link in `validation.md` | Dynamic `system-docs/DEPLOYMENT-SEO-GUIDE.md` generation |

---

## 3. Reused Features & Implementation Enhancements

### 3.1 Directly Reused Technical Patterns
1. **App Router & Pages Router Patterns**: We retained Next.js static/dynamic metadata definitions, `generateMetadata` (including Next.js 14 vs 15 `params` Promise distinctions), `sitemap.ts`, and `robots.ts` guidelines inside `references/nextjs-react.md`.
2. **JSON-LD XSS Escaping**: We strictly enforced `.replace(/</g, '\\u003c')` in all reference files to prevent script tag injection vulnerabilities.
3. **Ask-Before-Guessing & Placeholders**: We preserved the principle that business details must never be invented, enforcing `TODO` markers when details are unsupplied.

### 3.2 Key Phase 3.1 Enhancements Added in Our Skill
1. **Formal Route Classification Stage**: Enforced **"Discover first. Classify second. Audit third."** principle, categorizing routes into `page`, `api`, `admin`, `redirect`, `error`, `asset`, or `unknown`.
2. **REST/JSON API Exclusion**: API endpoints are strictly excluded from HTML sitemap generation, HTML meta tag audits, canonical tag checks, and Open Graph validation.
3. **API-Backed Frontend Pages Handling**: Distinguishes backend API dependencies (`/v1/cms-pages/about-us`) from frontend document pages (`/about`), targeting SEO page audits strictly at document pages.
4. **Deterministic SEO Page Candidate Rule**: Enforced strict eligibility criteria (`isDocumentPage AND isPublic AND notRedirect AND notError AND notStaticAsset`).
5. **Content Hash & Shared Layout Invalidation**: Retained SHA-256 route hashing and `globalSeoHash` invalidation for site-wide layout changes.
