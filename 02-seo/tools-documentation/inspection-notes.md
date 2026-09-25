# Technical Inspection Notes — `daniel-amekpoagbe/react-seo-skills`

**Date of Inspection**: 2026-09-25  
**Source Repository**: `https://github.com/daniel-amekpoagbe/react-seo-skills`  
**Inspected Location**: Scratch directory (`.../scratch/react-seo-skills`)

---

## 1. Overview & Repository Structure

The `react-seo-skills` repository is a framework-focused SEO skill designed for Cursor, Claude Code, and Codex.

### File Hierarchy in Base Repository
```text
react-seo-skills/
├── package.json
├── README.md
├── bin/
│   └── install.js
└── skill/
    ├── SKILL.md
    └── references/
        ├── app-router.md
        ├── astro.md
        ├── geo.md
        ├── keywords.md
        ├── language.md
        ├── pages-router.md
        ├── react-helmet-async.md
        ├── react-vite.md
        ├── structured-data.md
        └── validation.md
```

---

## 2. Key Findings & Breakdown

### 2.1 `SKILL.md` Structure
- Uses YAML frontmatter with `name` and `description`.
- Rules section includes language detection, stack detection, version checks, ask-for-details, avoiding duplicate SEO code, starting with keywords, validation, and performance checks.
- Defines **Detection Order** (Language -> Stack -> Implementation reference).
- Defines **Implementation Order** (Step 0 to Step 6).
- Defines an **SEO Audit Mode** returning findings formatted as Critical / Suggestion / OK.

### 2.2 Reference Files Analysis
- **`app-router.md`**: Covers static/dynamic `metadata` exports, `generateMetadata` (handling Next.js 14 vs 15 `params` Promise differences), root layout default metadata, `sitemap.ts`, `robots.ts`.
- **`pages-router.md`**: Covers `next/head`, custom `SEO` component pattern, `next-sitemap` config.
- **`react-vite.md`**: Covers Vite + React SPAs, `react-helmet-async`, client-side meta tags, `SEO` component encapsulation.
- **`astro.md`**: Covers Astro layouts, `@astrojs/sitemap`, frontmatter props.
- **`react-helmet-async.md`**: Deep dive into `react-helmet-async` setup and Provider rules.
- **`structured-data.md`**: Comprehensive Schema.org JSON-LD templates (`Organization`, `WebSite`, `LocalBusiness`, `BlogPosting`, `FAQPage`, `BreadcrumbList`) and safety rules (`.replace(/</g, '\\u003c')` XSS protection).
- **`keywords.md`**, **`geo.md`**, **`language.md`**, **`validation.md`**: Provide strategy, AI/GEO visibility tips, language extension rules (`.js`/`.ts`), and audit validation tools.

### 2.3 Framework Detection Logic
- Base detection relies on file system signals:
  - `app/layout.tsx|jsx` -> Next.js App Router
  - `pages/_app.tsx|jsx` -> Next.js Pages Router
  - `astro.config.*` -> Astro
  - `vite.config.*` -> Vite + React
- **Limitation**: Hardcoded exclusively around JS/TS React/Astro frameworks. Does not support non-JS full-stack or multi-framework setups like Vue/Nuxt, Angular, or Laravel.

### 2.4 SEO Audit Workflow
- Audits in base skill are stateless one-shot actions.
- Analyzes routes, inspects code for title/meta/OG/JSON-LD, checks sitemap/robots, and outputs Markdown reports.
- **Limitation**: No persistent audit memory or incremental tracking. Re-audits the entire codebase on every invocation.

### 2.5 Metadata / OG / JSON-LD Guidance
- Excellent code examples and best practices.
- Emphasizes explicit XSS escaping for JSON-LD.
- Strict rule against guessing business details, recommending placeholders or TODO comments if unsupplied.

---

## 3. Adaptations & Extensions for Our Architecture

| Base Concept | Base Skill Behavior | Our Skill Architecture Extension |
| :--- | :--- | :--- |
| **Framework Scope** | React (Next.js, Vite), Astro | Expanded to Next.js/React, Vue/Nuxt, Angular, and Laravel. Modularized via dedicated reference files. |
| **State & Memory** | Stateless (re-audits full site every run) | Introduced persistent `system-docs/seo-config.json` and `system-docs/seo-tracker.json` (with `schemaVersion`). |
| **Audit Workflow** | One-shot audit report | Two-phase workflow: **Initial Audit** (config + baseline tracker) vs **Subsequent Audit** (incremental diff tracking). |
| **Deployment Guidance** | General validation checklist in reference file | Dynamic `system-docs/DEPLOYMENT-SEO-GUIDE.md` generation combining tracker status with static GSC checklist. |
| **Config / State Separation** | Skill and project state mixed implicitly | Strict isolation: Reusable skill in `.agent/skills/seo/`, project state exclusively inside target project's `system-docs/`. |

---

## 4. Reusability Assessment

### Direct Reuse / Adaptation
1. Core SEO principles, Open Graph rules, canonical best practices, and JSON-LD XSS escaping from `structured-data.md` and `app-router.md` / `pages-router.md` / `react-vite.md`.
2. Ask-before-guessing and TODO placeholder rules.

### Major Modifications Needed
1. Decouple `SKILL.md` from specific framework code; transform `SKILL.md` into an agentic orchestrator that reads `seo-config.json` / `seo-tracker.json` and loads matching reference files on demand.
2. Structure framework references cleanly into `nextjs-react.md`, `vue-nuxt.md`, `angular.md`, `laravel.md`.
3. Separate core principles into `core-seo-concepts.md` and post-deploy into `gsc-post-deploy.md`.
