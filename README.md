# ⚡ AI Tools & Agent Skills Guide Repository

A curated, production-ready knowledge base and suite of reusable **AI Agent Skills**, design frameworks, and automated workflows for **Web Design & Frontend Development** and **Autonomous SEO Optimization**.

[![AI Skills](https://img.shields.io/badge/AI%20Skills-Agentic%20Orchestration-8A2BE2.svg)](./02-seo/)
[![Frameworks](https://img.shields.io/badge/Supported-Next.js%20%7C%20Vue%20%7C%20Angular%20%7C%20Laravel-blue.svg)]()
[![Documentation](https://img.shields.io/badge/docs-Markdown%20%26%20Word-orange.svg)](./downloads/)
[![Maintenance](https://img.shields.io/badge/status-active%20%26%20maintained-success.svg)]()

---

## 🗺️ Navigation & Guide Directory

| Category / Focus Area | 🚀 Start Here | 🎯 Purpose & Capabilities | 📚 Detailed Reference |
| :--- | :--- | :--- | :--- |
| 🎨 **Category 01: Web Design & Dev** | [`01-web-design-and-dev/README.md`](./01-web-design-and-dev/README.md) | Design rules, non-generic UI, visual UX audits & 3D workflows | [📖 Master Web Design Guide](./01-web-design-and-dev/ai-website-design-development-guide.md) |
| 🌐 **Category 02: SEO Agent Skill** | [`02-seo/README.md`](./02-seo/README.md) | Autonomous multi-stage route classification, SEO audits & sitemaps | [📖 SEO Skill Operating Guide](./02-seo/seo-skill-guide.md) |

---

## 🧭 Recommended User Workflow

```text
┌───────────────────────────┐
│ 1. Select Target Category │  ▶ Choose Web Design & Dev or SEO Optimization
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ 2. Open Category README   │  ▶ Review quick start triggers & project setup
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ 3. Execute Single Prompt  │  ▶ Trigger agent skill (e.g. "Audit the SEO of this project")
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ 4. Deep Reference Docs    │  ▶ Consult full reference guides when fine-tuning or troubleshooting
└───────────────────────────┘
```

---

# 🎨 Category 01 — Web Design & Frontend Engineering

> **Focus**: Modern UI/UX aesthetics, non-generic AI layouts, visual contrast audits & interactive 3D graphics  
> **Directory**: [`./01-web-design-and-dev/`](./01-web-design-and-dev/README.md)

> [!IMPORTANT]
> **Why Category 01 Matters**: Eliminates standard, repetitive "AI-generated" UI look-and-feels by enforcing strict architectural design rules (`DESIGN.md`), Taste skill prompts, automated visual UX audits (`Impeccable`), and procedural Three.js graphics.

### 🛠️ Core Web Design Toolchain

| Tool / Skill | Visual Focus | Key Capability | Quick Guide Link |
| :--- | :--- | :--- | :--- |
| 📐 **HagiCode Design** | Layout & Presets | Typography scale, color palette & layout structure rules | [📖 DESIGN.md Guide](./01-web-design-and-dev/tools-documentation/awesome-design-guide.md) |
| ✨ **Taste Skill** | Frontend Quality | Custom aesthetic rules to guarantee tailored, premium UI | [✨ Taste Skill Guide](./01-web-design-and-dev/tools-documentation/taste-skill-guide.md) |
| 🛡️ **Impeccable** | Visual UX Audit | Micro-spacing, contrast checks & component visual reviews | [🛡️ Impeccable Guide](./01-web-design-and-dev/tools-documentation/impeccable-guide.md) |
| 🔮 **img2threejs** | Interactive 3D | Converts 2D reference images into procedural Three.js 3D models | [🔮 img2threejs Guide](./01-web-design-and-dev/tools-documentation/img2threejs-guide.md) |

### 📚 Master References & Galleries
- 📖 [**Master AI Web Design & Development Guide**](./01-web-design-and-dev/ai-website-design-development-guide.md)
- 🖼️ [**Awesome Design MD Gallery Reference**](./01-web-design-and-dev/tools-documentation/awesome-design-md-gallery.md)

---

# 🌐 Category 02 — Reusable Autonomous SEO Agent Skill

> **Focus**: Portable AI agent skill, multi-stage route classification, automated fixes & incremental tracking  
> **Directory**: [`./02-seo/`](./02-seo/README.md) • **Skill Location**: `.agent/skills/seo/`

> [!TIP]
> **Single Prompt Trigger**  
> Run your AI coding assistant from the target project root and say:  
> **`"Audit the SEO of this existing project."`**

### ⚙️ Regression-Tested Framework Support

| Framework / Stack | Router & Rendering | SEO Features Handled | Dedicated Reference |
| :--- | :--- | :--- | :--- |
| ⚛️ **Next.js / React** | App Router & Pages Router | Static/dynamic `metadata`, async `params`, `sitemap.ts`, `robots.ts` | [📖 Next.js Guide](./02-seo/.agent/skills/seo/references/nextjs-react.md) |
| 🟢 **Vue / Nuxt** | Nuxt 3 / Vue 3 | `useSeoMeta`, `@nuxtjs/sitemap`, Unhead SSR head tag injection | [📖 Vue/Nuxt Guide](./02-seo/.agent/skills/seo/references/vue-nuxt.md) |
| 🅰️ **Angular** | Angular SSR & Hydration | `Title` & `Meta` services, server-rendered head tags | [📖 Angular Guide](./02-seo/.agent/skills/seo/references/angular.md) |
| 🔴 **Laravel** | Blade & Inertia.js | Blade SEO partials, Inertia `<Head>`, Spatie XML sitemap generator | [📖 Laravel Guide](./02-seo/.agent/skills/seo/references/laravel.md) |

### 📁 Project-Specific State Architecture (`system-docs/`)

All audited project state is persisted exclusively inside the target project's `system-docs/` folder:

- ⚙️ **`system-docs/seo-config.json`**: Detected framework, router, language, rendering mode, and domain configuration.
- 📊 **`system-docs/seo-tracker.json`**: Route inventory, multi-stage classifications (`page`, `api`, `auth`), SHA-256 `contentHash`, and global status.
- 📑 **`system-docs/DEPLOYMENT-SEO-GUIDE.md`**: Dynamically generated Google Search Console & deployment guide.

> [!NOTE]
> **Production Status**: **Stable & Ready for Use**. Uses deterministic classification pipelines, SHA-256 content hashing, and false-fix protections.

---

## 📦 Installation & Download Guide

### ⚡ Option 1: Copy Agent Skills to Your Project
To equip any repository with the SEO Agent Skill, copy the `.agent` folder into your project root:
```bash
cp -r 02-seo/.agent /path/to/my-project/
```

### 💻 Option 2: Clone the Knowledge Repository
```bash
git clone https://github.com/khademul-menaliam/Ai-tools-guide-documentation.git
```

### 📄 Option 3: Offline Document Download
Offline Word (`.docx`) versions of all guides are archived in the [`downloads/`](./downloads/) directory for offline viewing.

---

## 🤝 Contributing & Feedback

Contributions, suggestions, and new agent skill additions are welcome! Open an issue or submit a pull request.



