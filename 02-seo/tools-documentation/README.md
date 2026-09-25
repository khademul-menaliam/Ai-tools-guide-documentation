# Tools Documentation Index — SEO Module

This directory contains technical documentation, inspection notes, and analysis guides relating to external tools and foundation repositories used to design our SEO skill.

---

## Documentation Index

| File | Description |
| :--- | :--- |
| [inspection-notes.md](inspection-notes.md) | Initial technical inspection notes from cloning and inspecting `daniel-amekpoagbe/react-seo-skills`. |
| [react-seo-skills-guide.md](react-seo-skills-guide.md) | Technical reference guide analyzing the base `react-seo-skills` repository and detailing our Phase 3.1 architectural adaptations. |

---

## Key Takeaways

1. **Foundation**: We used `daniel-amekpoagbe/react-seo-skills` as a technical starting point for metadata, Open Graph, and JSON-LD safety patterns.
2. **Phase 3.1 Reliability Enhancements**: We extended the foundation into a portable, agentic, multi-framework SEO system that enforces formal Route Classification (**"Discover first. Classify second. Audit third."**), REST/JSON API endpoint exclusion, API-backed frontend page handling, stateful tracking via `system-docs/` (`seo-config.json`, `seo-tracker.json`), content-hash-based change detection (`sha256:`), shared SEO file invalidation (`globalSeoHash`), legacy tracker migration and backfilling, conservative rendering mode detection (`SSR`, `SSG`, `CSR`, `Hybrid`, `Unknown`), automatic deleted-route cleanup, incremental auditing, and dynamic deployment guide generation.
