# SEO Agent Skill — Operating & Implementation Guide

## 1. Purpose

The SEO Agent Skill is a portable, reusable workflow for auditing and maintaining SEO in web projects.

Skill location:

```text
02-seo/.agent/skills/seo/
```

The user-facing entry point is:

```text
02-seo/README.md
```

This document explains how the Skill operates, what it creates, and how to maintain it.

---

## 2. Skill architecture

```text
02-seo/.agent/skills/seo/
├── SKILL.md
└── references/
    ├── core-seo-concepts.md
    ├── nextjs-react.md
    ├── vue-nuxt.md
    ├── angular.md
    ├── laravel.md
    └── gsc-post-deploy.md
```

### `SKILL.md`

The main orchestrator. It defines the rules, workflows, route classification, audit behavior, fixing behavior, tracking, and reporting.

### `references/`

Framework-specific and reusable SEO knowledge loaded when required.

---

## 3. User workflow

### Workflow 1 — Initial audit

User prompt:

```text
Audit the SEO of this existing project.
```

The Skill should:

1. Inspect the target project.
2. Detect framework, router, language, and rendering strategy.
3. Load the appropriate framework reference.
4. Discover routes.
5. Classify every discovered route.
6. Identify public SEO page candidates.
7. Audit SEO requirements.
8. Apply safe deterministic fixes automatically.
9. Request only genuinely missing information.
10. Update `seo-config.json`.
11. Update `seo-tracker.json`.
12. Generate/update `DEPLOYMENT-SEO-GUIDE.md`.
13. Run available verification/build checks.
14. Produce the final state-driven audit report.

The deployment guide is generated even when no code fixes were necessary.

---

## 4. Workflow 2 — Incremental audit

After the initial baseline, use:

```text
Audit the SEO again.
```

The Skill loads the previous state and compares the current project with the tracker.

Each tracked route is classified as:

```text
NEW
MODIFIED
UNCHANGED
DELETED
```

### NEW

A discovered route that does not exist in the tracker.

Action:

- audit it
- fix safe issues
- add it to the tracker

### MODIFIED

A tracked route whose deterministic content hash changed.

Action:

- re-audit it
- fix safe issues
- update its hash and audit state

### UNCHANGED

A tracked route whose relevant content hash has not changed.

Action:

- do not unnecessarily repeat the full route audit

### DELETED

A route present in the tracker but no longer present in the discovered route inventory.

Action:

- confirm it is actually absent
- remove it from the active tracker
- recalculate summary counts
- report the deleted route

---

## 5. Content hashing

Route change detection uses SHA-256.

Stored form:

```text
sha256:<64 hexadecimal characters>
```

The hash is based on the relevant route implementation files and their paths, using deterministic ordering.

This avoids treating a route as changed merely because a timestamp changed after operations such as cloning or checkout.

If hashing fails, the Skill must not silently treat the route as unchanged.

---

## 6. Rendering detection

Supported classifications:

```text
SSR
SSG
CSR
Hybrid
Unknown
```

The Skill uses framework-specific evidence.

Examples:

### Next.js

Checks App Router / Pages Router and relevant static/server/client patterns.

### Nuxt

Checks Nuxt SSR configuration, prerendering, and route rules.

### Angular

Checks Angular SSR/server entry points and hydration configuration.

### Laravel

Distinguishes Blade server rendering, Inertia/hybrid rendering, and API-driven client rendering.

If evidence is insufficient:

```json
{
  "rendering": "Unknown",
  "renderingDetectedAutomatically": false
}
```

The Skill must not guess.

If a user has explicitly overridden the rendering mode, automatic detection must respect the override.

---

## 7. Route classification

The Skill must classify routes before deciding whether they belong in the public sitemap.

Typical public SEO candidates include:

- Home
- About
- Services
- Service detail pages
- Blog/content pages
- Contact
- Public product/event pages

Typical excluded categories include:

- REST/API endpoints
- Authentication
- Admin/private pages
- Booking/checkout/transaction pages
- Post-action confirmation/success pages
- Storage/fallback routes
- Error handlers

The exact categories depend on the project.

### Important rule

Do not hard-code category counts.

Counts must be calculated from the actual route inventory/tracker.

The following reconciliation must hold:

```text
SEO page candidates + excluded routes = total discovered routes
```

And excluded category totals must reconcile to the total excluded routes.

---

## 8. Sitemap and robots behavior

The Skill should include only appropriate public SEO pages in the sitemap.

Private, transactional, authentication, API, and utility routes should not be added merely because they are technically reachable.

Framework-native sitemap and robots mechanisms should be preferred where available.

A dynamic route pattern such as:

```text
/events/[id]
```

represents a route category, not necessarily one literal sitemap URL.

If the project obtains concrete records from a backend/API, the sitemap may expand those records into concrete URLs.

Example:

```text
/events/[id]
        ↓
/events/summer-conference
/events/design-workshop
/events/product-launch
```

If no concrete records are available, the Skill should report the limitation rather than invent URLs.

---

## 9. Production domain safety

The production frontend domain must never be guessed.

When it is missing, the Skill should ask for it.

Example:

```text
https://example.com
```

Do not confuse:

```text
Frontend:
https://example.com

Backend/API:
https://api.example.com
```

The frontend production origin is used for canonical URLs, Open Graph URLs, Organization metadata, sitemap URLs, robots sitemap declarations, and related production SEO configuration.

---

## 10. Automatic fixing rules

Safe deterministic issues should be fixed automatically.

Examples:

- missing title metadata when the correct page title can be determined
- missing meta description when deterministic content is available
- missing canonical configuration when the production origin is known
- missing Open Graph defaults
- missing robots configuration
- missing sitemap configuration
- deterministic heading hierarchy fixes
- deterministic image accessibility fixes
- safe structured-data implementation

The Skill should not invent business claims, brand facts, or marketing copy that require user knowledge.

When information is genuinely required, ask the user.

---

## 11. Project state files

After an audit, the target project contains:

```text
system-docs/
├── seo-config.json
├── seo-tracker.json
└── DEPLOYMENT-SEO-GUIDE.md
```

### `seo-config.json`

Stores project-level state.

Important fields include:

```text
project.name
project.domain
project.framework
project.router
project.language
project.rendering
project.renderingDetectedAutomatically
project.detectedAutomatically
project.userOverridden
```

Settings may include locale, sitemap path, robots path, and other SEO configuration.

### `seo-tracker.json`

Stores audit history.

Important route fields include:

```text
status
lastAudited
contentHash
issuesResolved
pendingIssues
```

Global status tracks items such as:

```text
sitemap
robots
gscVerified
cwvChecked
```

The summary tracks:

```text
totalRoutes
completedRoutes
pendingRoutes
```

The tracker is project-specific and must remain isolated inside the target project.

---

## 12. Deployment guide automation

`system-docs/DEPLOYMENT-SEO-GUIDE.md` is generated/updated automatically by the audit workflows.

It should reflect current project state and include only relevant deployment information.

Typical sections:

1. Production configuration
2. Google Search Console verification
3. Sitemap submission
4. Robots verification
5. Indexing checks
6. Structured-data verification
7. Core Web Vitals monitoring
8. Remaining project-specific warnings

The guide is generated even when the audit found zero code changes.

---

## 13. Final reporting

The final report must be state-driven.

Preferred format:

```text
SEO AUDIT COMPLETE

Status:
- Automatically fixed: X
- Remaining issues: X
- Information required: X
- Permission required: Yes/No

Next step:
- ...
```

Do not report a generic success message when an actual issue or required user input remains.

---

## 14. Verification and regression standard

The Skill has been tested across:

```text
Next.js / React
Vue / Nuxt
Angular
Laravel
```

Regression coverage includes:

- initial audit
- incremental audit
- route change detection
- deleted route cleanup
- content hashing
- rendering detection
- route-category reconciliation
- automatic deployment-guide generation
- production-domain safety
- dynamic sitemap behavior
- final acceptance testing

Current acceptance state:

```text
Core implementation: PASS
Validation evidence: PASS
Framework regression: PASS
Incremental regression: PASS
Route reconciliation: PASS
Deployment-guide automation: PASS
Production workflow: PASS
Dynamic sitemap verification: PASS
Final acceptance test: PASS

Reusable Skill defects: 0
Known customer/project defects from testing: 0
Blockers: 0

STATUS: STABLE / READY FOR USE
```

---

## 15. Maintenance rule

Do not create new roadmap phases simply to extend the project.

A Skill change should normally happen only when:

1. a real reproducible defect is found,
2. a framework regression appears,
3. a required capability is missing, or
4. a documented requirement changes.

When a defect is found:

```text
Reproduce
→ identify whether it is Skill-wide or project-specific
→ patch the reusable Skill if appropriate
→ regression-test affected frameworks/workflows
→ update documentation
→ record final PASS/FAIL status
```

---

## 16. Quick command/prompt reference

Run from the target project root:

```text
Audit the SEO of this existing project.
```

After changes:

```text
Audit the SEO again.
```

To apply already-identified fixes:

```text
Fix the SEO issues you identified.
```

For the deployment guide on demand:

```text
Generate/update the deployment SEO guide.
```

The normal audit workflows already generate/update the deployment guide automatically.

---

## 17. One-screen mental model

```text
INSTALL SKILL
    ↓
Audit the SEO of this existing project.
    ↓
Detect project + routes
    ↓
Classify routes
    ↓
Audit SEO
    ↓
Auto-fix safe issues
    ↓
Ask only for missing information
    ↓
Update config + tracker
    ↓
Generate deployment guide
    ↓
Verify
    ↓
SEO AUDIT COMPLETE

Later:
Project changes
    ↓
Audit the SEO again.
    ↓
NEW / MODIFIED / UNCHANGED / DELETED
    ↓
Re-audit only what needs attention
    ↓
Update tracker + deployment guide
```
