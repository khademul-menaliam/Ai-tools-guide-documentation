# 02 — SEO Module & Agent Skill

Welcome to the **SEO Module** of the `Ai-tools-guide` repository.

This directory houses our portable, agentic **SEO Skill** (`.agent/skills/seo/`) and comprehensive documentation for deploying autonomous SEO workflows across web development projects.

---

## Directory Overview

```text
02-seo/
├── README.md                           # Main SEO module documentation (this file)
├── seo-skill-guide.md                  # Complete operating guide for the SEO Skill
│
├── .agent/                             # Agent Skills Container (copyable into any project)
│   └── skills/
│       └── seo/
│           ├── SKILL.md                # Orchestration skill definition & workflow controller
│           └── references/             # Framework and concept reference files
│               ├── core-seo-concepts.md
│               ├── nextjs-react.md
│               ├── vue-nuxt.md
│               ├── angular.md
│               ├── laravel.md
│               └── gsc-post-deploy.md
│
└── tools-documentation/
    ├── README.md                       # Tools documentation index
    ├── inspection-notes.md             # Inspection notes from base react-seo-skills repo
    └── react-seo-skills-guide.md       # Comparative guide & technical analysis of base skill vs our skill
```

---

## Base `react-seo-skills` vs Our Extended Skill

We used [`daniel-amekpoagbe/react-seo-skills`](https://github.com/daniel-amekpoagbe/react-seo-skills) as our base technical reference. Below is a comparison highlighting how our skill builds upon and extends the base foundation:

| Feature / Dimension | Base `react-seo-skills` Repo | Our Extended SEO Agent Skill (Phase 3.1.1 Hardened) |
| :--- | :--- | :--- |
| **Supported Frameworks** | Next.js (App & Pages Router), Astro, Vite + React | Next.js/React, Vue/Nuxt, Angular, Laravel (extensible architecture) |
| **Route Classification** | Basic / Assumptions | Formal Classification (`page`, `api`, `admin`, `redirect`, `error`, `asset`, `unknown`) |
| **API Endpoint Handling** | None (could treat API as HTML) | REST/JSON API endpoints explicitly excluded from HTML sitemaps and meta audits |
| **Audit State Memory** | None (stateless; re-audits whole codebase every run) | Incremental tracking via `system-docs/seo-tracker.json` (`schemaVersion: "1.0"`) |
| **Change Detection** | Timestamp / None | Deterministic SHA-256 content hashing (`contentHash: "sha256:..."`) |
| **Shared SEO Invalidation** | None | Changes to root layouts (`app/layout.tsx`) invalidate dependent route hashes (`globalSeoHash`) |
| **Legacy Tracker Migration** | None | Backfills missing `contentHash` entries automatically during baseline re-audit |
| **Deleted Route Cleanup** | None | Automatic comparison of discovered routes vs tracker; cleans up deleted routes |
| **Rendering Detection** | Basic / Implied | Conservative classification (`SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown`) in `seo-config.json` |
| **Project Environment Config** | None | Environment config via `system-docs/seo-config.json` (`schemaVersion: "1.0"`) |
| **Deployment Guidance** | Static validation checklist | Dynamic `system-docs/DEPLOYMENT-SEO-GUIDE.md` combining live state + GSC instructions |
| **Portability** | Standalone npm package / Cursor skill | Native `.agent/skills/seo/` format compatible with AI agent conventions |

---

## How to Install / Copy the Skill into a Project

To equip any client or target repository with this SEO skill, copy the `.agent` directory into the root of the target project:

```bash
# Example: Copying the skill into a target project
cp -r 02-seo/.agent /path/to/target-project/
```

Once placed at `/path/to/target-project/.agent/skills/seo/`, compatible AI coding agents will automatically discover the skill and utilize its instructions during SEO tasks.

---

## System-Docs State Architecture

The skill maintains complete separation between reusable skill instructions and project-specific state. All target project data is stored inside `system-docs/` in the target project root:

1. **`system-docs/seo-config.json`**: Stores detected/configured framework, router, language, rendering mode (`SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown`), and site settings. Uses `"schemaVersion": "1.0"`.
2. **`system-docs/seo-tracker.json`**: Tracks audited routes, classification properties (`routeType`, `responseType`, `isSeoPageCandidate`), SHA-256 `contentHash`, `globalSeoHash`, completed fixes, pending items, timestamps, and global SEO status. Uses `"schemaVersion": "1.0"`.
3. **`system-docs/DEPLOYMENT-SEO-GUIDE.md`**: Dynamically generated post-launch guide combining real tracker state with GSC verification steps.

---

## Summary of Workflows

1. **Initial Baseline Audit**: Detects project stack & rendering -> Asks user for missing business details -> Writes `seo-config.json` -> Classifies routes (`page`, `api`, `admin`, `redirect`, `error`, `asset`) -> Builds verified `isSeoPageCandidate` inventory -> Computes baseline content hashes -> Audits global & page SEO -> Generates sitemap for HTML pages -> Writes `seo-tracker.json` -> Reports summary.
2. **Subsequent Incremental Audit**: Reads `seo-config.json` & `seo-tracker.json` -> Re-classifies routes -> Checks legacy hashes & shared layout invalidation -> Computes current content hashes -> Classifies routes (`NEW`, `MODIFIED`, `UNCHANGED`, `DELETED`) -> Cleans up deleted routes -> Audits only modified/new/incomplete `isSeoPageCandidate` pages -> Updates sitemap & `seo-tracker.json` -> Reports progress.
3. **Deployment Guide Generation**: Generates `system-docs/DEPLOYMENT-SEO-GUIDE.md` on demand.

For full step-by-step operating details, consult [seo-skill-guide.md](seo-skill-guide.md).
