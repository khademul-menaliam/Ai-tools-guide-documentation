# 🚀 AI Tools & Skills Guide Repository

A practical, curated collection of reusable AI-assisted workflows, skills, and developer tools for **Web Design & Development** and **SEO Optimization**.

---

## 📌 Repository Directory & Guides

| Category / Area | Start Here | Purpose / Scope | Detailed Reference |
| :--- | :--- | :--- | :--- |
| 🎨 **Web Design & Development** | [`01-web-design-and-dev/README.md`](./01-web-design-and-dev/README.md) | Design systems, UI quality, frontend tools, and full site workflows | [📖 Master Web Design Guide](./01-web-design-and-dev/ai-website-design-development-guide.md) |
| 🔍 **SEO Agent Skill** | [`02-seo/README.md`](./02-seo/README.md) | Autonomous, framework-aware SEO auditing, fixing & sitemap generation | [📖 SEO Skill Operating Guide](./02-seo/seo-skill-guide.md) |

---

## 🔄 Recommended Workflow

1. **Choose your focus area** (Web Design & Dev or SEO).
2. **Open that section's `README.md`** to review the quick user workflow.
3. **Execute the workflow** with your AI coding assistant using single-prompt triggers.
4. **Consult detailed reference guides** only when you need deep implementation, AST rules, or troubleshooting instructions.

---

## 🎨 Category 01: Web Design & Development with AI

A complete toolchain and guide for building polished, modern web applications with AI coding agents:

- **HagiCode Design**: Layout, aesthetic, color palette, and typography planning.
- **Taste Skill**: Enforces custom design rules to eliminate generic AI UI output.
- **Impeccable**: Automated UX visual audits, contrast checks, and component refinement.
- **img2threejs**: Converts 2D reference images into procedural Three.js 3D models.

### Key Tools & Guides
- [📖 Master AI Web Design & Dev Guide](./01-web-design-and-dev/ai-website-design-development-guide.md)
- [📐 Awesome DESIGN.md Guide](./01-web-design-and-dev/tools-documentation/awesome-design-guide.md)
- [✨ Taste Skill Guide](./01-web-design-and-dev/tools-documentation/taste-skill-guide.md)
- [🔍 Impeccable Guide](./01-web-design-and-dev/tools-documentation/impeccable-guide.md)
- [🧊 img2threejs Guide](./01-web-design-and-dev/tools-documentation/img2threejs-guide.md)

---

## 🔍 Category 02: Reusable SEO Agent Skill

A portable, agentic SEO skill located at `.agent/skills/seo/` designed to be copied directly into target project repositories for autonomous SEO auditing, remediating, and tracking.

### Quick Start Prompt
From the target project root, simply tell your AI assistant:
> *"Audit the SEO of this existing project."*

### Regression-Tested Stack Support
- ⚡ **Next.js / React** (App Router & Pages Router, async `params`)
- 💚 **Vue / Nuxt** (Unhead / `useSeoMeta`)
- 🅰️ **Angular** (`Title` & `Meta` services, SSR)
- 🐘 **Laravel** (Blade templates, Inertia.js, Spatie Sitemap)

### Project-Specific State Architecture
The Skill maintains project state exclusively inside the target repository's `system-docs/` folder:
- `system-docs/seo-config.json`: Detected framework, router, language, and rendering strategy.
- `system-docs/seo-tracker.json`: Incremental audit route inventory, `contentHash`, and global SEO status.
- `system-docs/DEPLOYMENT-SEO-GUIDE.md`: Dynamically generated Google Search Console & deployment guide.

> **Status**: **Stable & Production-Ready**. New changes are driven strictly by reproducible codebase defects or required capabilities.

---

## 📥 How to Use & Download

### Option 1: View Online
Browse any `.md` file directly on GitHub for formatted text, tables, and clickable references.

### Option 2: Copy Skills to Your Project
To equip any repository with the SEO skill, copy the `.agent` folder to your target project root:
```bash
cp -r 02-seo/.agent /path/to/my-project/
```

### Option 3: Clone the Repository
```bash
git clone https://github.com/khademul-menaliam/Ai-tools-guide-documentation.git
```

### Option 4: Download Offline Docs
Offline Word (`.docx`) documents are archived in the [`downloads/`](./downloads/) directory.

---

## 🤝 Contributing

Contributions, fixes, and new tool suggestions are welcome! Feel free to open an issue or pull request.

