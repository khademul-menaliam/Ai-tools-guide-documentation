# SEO Agent Skill

Reusable SEO auditing and fixing Skill for web projects.

## Start here

You normally only need **one prompt**:

> **Audit the SEO of this existing project.**

The Skill will inspect the project, detect the framework and routes, audit SEO, automatically fix safe deterministic issues, track the result, and create/update the deployment guide.

---

## 1. Normal user workflow

### First audit

Run your AI coding assistant from the **target project root** and say:

> Audit the SEO of this existing project.

The Skill will:

1. Detect the framework, router, language, and rendering mode.
2. Discover application routes.
3. Classify routes into SEO pages and non-SEO routes.
4. Check metadata, canonical URLs, Open Graph, structured data, headings, image accessibility, sitemap, robots, and related SEO requirements.
5. Automatically fix safe/deterministic issues.
6. Ask only for information it genuinely cannot determine.
7. Create/update:
   - `system-docs/seo-config.json`
   - `system-docs/seo-tracker.json`
   - `system-docs/DEPLOYMENT-SEO-GUIDE.md`
8. Run the relevant verification/build checks.
9. Report what was fixed and what remains.

### If the Skill asks for the production domain

Provide the **frontend production website URL**, for example:

```text
https://example.com
```

Do not provide a backend/API URL when the Skill is asking for the frontend website domain.

### After future project changes

Use:

> Audit the SEO again.

The Skill compares the current route/source state with the tracker and handles:

- `NEW` routes
- `MODIFIED` routes
- `UNCHANGED` routes
- `DELETED` routes

It uses route content hashing so unchanged routes are not unnecessarily re-audited.

### If you already have identified SEO issues

Use:

> Fix the SEO issues you identified.

The Skill fixes safe issues, updates the tracker, and updates the deployment guide.

---

## 2. What the Skill handles automatically

### Technical SEO

- Title and meta description
- Canonical URL
- Open Graph metadata
- Social metadata
- Robots directives
- Sitemap
- Structured data / JSON-LD
- Heading hierarchy
- Image `alt` accessibility checks
- Route classification
- Public vs private/transactional route handling
- Production-domain configuration

### Route intelligence

The Skill does not treat every URL as a public SEO page.

It distinguishes public SEO pages from examples such as:

- Authentication
- Admin/private pages
- Checkout/booking/transaction flows
- Post-action confirmation/success pages
- API endpoints
- Storage/fallback routes
- Error handlers

Counts in reports and deployment guides are derived from the actual tracker rather than hard-coded.

### Incremental audits

The tracker stores SHA-256 route content hashes.

That allows the Skill to detect whether a route is:

```text
NEW
MODIFIED
UNCHANGED
DELETED
```

Deleted routes are removed from the active tracker after confirmation.

### Rendering detection

The Skill detects rendering strategy where framework evidence supports it:

```text
SSR
SSG
CSR
Hybrid
Unknown
```

If evidence is insufficient, it does not invent a rendering mode.

A manual rendering override is respected.

---

## 3. Files created in a customer project

After an audit, look in:

```text
system-docs/
├── seo-config.json
├── seo-tracker.json
└── DEPLOYMENT-SEO-GUIDE.md
```

### `seo-config.json`

Stores project-level SEO configuration such as:

- project name
- production domain
- framework
- router
- language
- rendering mode
- SEO settings

### `seo-tracker.json`

Stores:

- discovered routes
- route status
- last audit time
- route content hash
- resolved issues
- pending issues
- sitemap/robots status
- audit summary

### `DEPLOYMENT-SEO-GUIDE.md`

A project-specific go-live guide generated automatically by the Skill.

It normally contains:

- production-domain requirements
- Google Search Console verification
- sitemap submission
- robots verification
- indexing checks
- Core Web Vitals follow-up
- structured-data verification
- remaining project-specific warnings

You do **not** need to ask separately for this guide after an audit.

---

## 4. Final audit result

A completed audit ends with a short state-driven result such as:

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

If nothing remains, the Skill should say so directly.

---

## 5. Supported framework regression coverage

The reusable Skill has been regression-tested for:

- Next.js / React
- Vue / Nuxt
- Angular
- Laravel

The framework reference files are located at:

```text
02-seo/.agent/skills/seo/references/
```

---

## 6. Installing the Skill in a project

Copy the `.agent` directory from:

```text
02-seo/.agent/
```

into the root of the target project.

The target project should then contain:

```text
my-project/
└── .agent/
    └── skills/
        └── seo/
            ├── SKILL.md
            └── references/
```

Then run the normal audit prompt from the target project's root.

---

## 7. Stable status

The SEO Skill has completed:

- Core implementation
- Validation evidence
- Framework regression
- Incremental regression
- Route-category reconciliation
- Deployment-guide automation
- Production workflow verification
- Dynamic sitemap verification
- Final acceptance testing

Current status:

**STABLE / READY FOR USE**

No additional development phase is required unless a real reproducible defect is discovered.
