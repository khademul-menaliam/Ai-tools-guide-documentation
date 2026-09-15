# Taste Skill — Complete Documentation Guide

## 1. Overview

**Taste Skill** is an open-source collection of portable SKILL.md instructions designed to help AI coding agents create better frontend interfaces.

Its main purpose is to reduce generic, repetitive, or "template-like" AI-generated frontend designs and encourage stronger decisions around:

- Layout

- Typography

- Spacing

- Colors

- Visual hierarchy

- Motion and animation

- Responsive design

- Component styling

- Design consistency

The project describes itself as an **"Anti-Slop Frontend Framework for AI Agents."** It is not a traditional frontend framework like React, Vue, Bootstrap, or Tailwind CSS. Instead, it gives AI coding agents additional design instructions to follow while creating or modifying a frontend. (GitHub)

Official website:

https://www.tasteskill.dev/

Official GitHub repository:

https://github.com/Leonxlnx/taste-skill

License: **MIT**. (GitHub)

## 2. What Is Taste Skill?

Taste Skill is a collection of **AI-agent skills**.

Each skill is represented by a SKILL.md file containing instructions that an AI coding agent can read and follow.

The important concept is:

**Taste Skill does not build the website by itself. It tells an AI coding agent how to approach the design and implementation of the website.**

For example, instead of simply asking an AI:

Build a landing page for my application.

you can use Taste Skill and provide a more detailed brief:

Build a landing page for my finance application.

Audience:

Young professionals.

Style:

Premium, modern, clean and trustworthy.

Colors:

Navy and green.

Avoid:

Generic SaaS layouts and excessive gradients.

The AI agent uses the Taste Skill instructions together with your requirements to produce the frontend.

## 3. How Does Taste Skill Work?

The basic concept is:

Your Requirements

↓

AI Coding Agent

↓

Taste Skill Instructions

↓

Design Analysis

↓

Design Direction

↓

Frontend Implementation

↓

UI Review

↓

Final Frontend

Taste Skill is **framework agnostic**, so the design rules can be applied to technologies such as React, Vue, Svelte, and other frontend environments. (GitHub)

## 3.1 The AI Reads the Skill

A SKILL.md file contains instructions for the AI agent.

The agent can use these instructions when creating or modifying the project.

For example:

SKILL.md

↓

AI reads design rules

↓

AI applies the rules

↓

Frontend code is generated

There is no separate Taste Skill server that has to run in the background.

The important part is that the **AI coding agent has access to the skill instructions**. (Taste Skill)

## 4. Taste Skill Has Multiple Skills

This is an important part of understanding Taste Skill.

**You do not have to use every skill.**

Each skill has a specific purpose.

The current repository includes several different skills. (GitHub)

### Main Skills

| Skill | Purpose |
| --- | --- |
| design-taste-frontend | Main Taste Skill v2 for general frontend design |
| design-taste-frontend-v1 | Original v1 version |
| gpt-taste | Stricter version intended for GPT/Codex workflows |
| image-to-code | Image/reference-first frontend workflow |
| redesign-existing-projects | Audits and improves an existing frontend |
| soft-skill | Softer, calmer, premium visual style |
| minimalist-skill | Minimalist interface design |
| brutalist-skill | Brutalist/industrial design direction |
| Image-generation skills | Generate visual references or design boards |

The exact list can change as the repository evolves, so the GitHub repository should be treated as the current source of truth. (GitHub)

## 5. Which Skill Should You Use?

You choose the skill based on the task.

### Creating a new website

Use:

design-taste-frontend

This is the current default **v2 experimental** skill. (Taste Skill)

### Creating a frontend specifically with Codex/GPT

Use:

gpt-taste

This version has stronger layout variation and stronger anti-generic design direction for GPT/Codex workflows. (GitHub)

### Converting a visual reference into a website

Use:

image-to-code

This is intended for an image/reference-first workflow where visual references are analyzed and then implemented in code. (GitHub)

### Improving an existing website

Use:

redesign-existing-projects

This workflow first audits the existing interface and then focuses on improving layout, spacing, hierarchy, styling, and related design issues. (GitHub)

## 6. The Three Main Design Controls

The main Taste Skill has three configurable design "dials."

These are numbers from **1 to 10**. (GitHub)

## DESIGN_VARIANCE

Controls how experimental the layout should be.

1–3  → Clean, centered, conventional

4–7  → More variation and overlapping elements

8–10 → Highly experimental / asymmetric

Example:

DESIGN_VARIANCE: 3

would encourage a more conventional layout.

While:

DESIGN_VARIANCE: 9

would encourage more experimental layouts.

## MOTION_INTENSITY

Controls the amount of animation and interaction.

1–3  → Very subtle animation

4–7  → Normal animation and transitions

8–10 → Strong interaction and advanced motion

For example:

MOTION_INTENSITY: 2

could be suitable for a professional dashboard.

While:

MOTION_INTENSITY: 8

could be suitable for a creative portfolio or marketing website.

## VISUAL_DENSITY

Controls how much information appears within the viewport.

1–3  → Spacious / minimal

4–7  → Normal information density

8–10 → Dense / information-heavy

For example:

VISUAL_DENSITY: 2

could be suitable for a luxury landing page.

While:

VISUAL_DENSITY: 9

could be useful for a dashboard with many tables, statistics, and controls.

(GitHub)

## 7. Different Ways to Use Taste Skill

There is **not only one way to use Taste Skill**.

There are several supported approaches.

## Option 1 — Install the Main Skill

This is the simplest option when you only want the main Taste Skill.

Run:

npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"

This installs the current **v2 experimental** version of the main skill. The agent can then read the installed SKILL.md automatically. No additional configuration is required. (Taste Skill)

# Option 2 — Install the Full Skill Bundle

If you want access to all the skills in the repository:

npx skills add Leonxlnx/taste-skill

This installs the available skills from the repository. (Taste Skill)

This is useful if you regularly work on different types of projects.

For example:

New website

↓

design-taste-frontend

Codex-specific work

↓

gpt-taste

Image → Website

↓

image-to-code

Existing website redesign

↓

redesign-existing-projects

You don't need to use all of them on every project.

# Option 3 — Install the Skills for Codex

If you are using Codex, you can install the full skill bundle into the Codex skills directory:

npx skills add Leonxlnx/taste-skill -a codex

The official documentation specifically provides this installation method for Codex. (Taste Skill)

After installation, Codex can use the available skills when working on your projects.

# Option 4 — Copy a SKILL.md Manually

You do not necessarily have to use the npx skills command.

The repository also allows you to copy a specific SKILL.md file into your project. (GitHub)

For example:

my-project/

├── src/

├── public/

├── package.json

└── SKILL.md

Then tell your AI agent to read and follow the file.

Example:

Read SKILL.md and follow its design rules while building this project.

This is useful when you want the design instructions to remain directly inside a specific project.

# Option 5 — Paste the Skill Into an AI Conversation

The repository also states that a SKILL.md can be pasted into ChatGPT or Codex conversations. (GitHub)

This can be useful for quick testing.

For example:

Here is the Taste Skill instruction.

[SKILL.md content]

Now use these rules to design my landing page.

This does not require a permanent installation.

## 8. Recommended Way to Use It

For normal development, the easiest approach is:

Install the skill

↓

Open your frontend project

↓

Open Codex / Claude Code / another compatible agent

↓

Give the AI your project requirements

↓

Tell it to use the installed Taste Skill

↓

AI designs and implements the UI

You do **not** need to manually execute Taste Skill every time.

Once the skill is installed and available to the agent, the agent can read the SKILL.md instructions when appropriate. (Taste Skill)

## 9. Example: Creating a New Website

Suppose you want to build a finance application.

You could give the AI this brief:

Use Taste Skill to design and build this frontend.

Project:

Personal finance management application.

Features:

- Income tracking

- Expense tracking

- Savings goals

- Transaction history

- Financial dashboard

Target audience:

Young professionals.

Visual direction:

Premium, modern, clean and trustworthy.

Colors:

Deep navy with green accents.

Typography:

Modern and highly readable.

Motion:

Subtle and professional.

Layout:

Clean but not generic.

Avoid:

- Generic SaaS dashboard layouts

- Excessive gradients

- Too many cards

- Unnecessary animations

- Repetitive sections

The AI can then use the Taste Skill rules while implementing the frontend.

## 10. Example: Using the GPT/Codex Variant

If you are working primarily with Codex, you can use:

gpt-taste

Install:

npx skills add https://github.com/Leonxlnx/taste-skill --skill "gpt-taste"

Then provide your requirements:

Use the gpt-taste skill.

Create a premium landing page for my finance application.

The design should be modern, visually interesting,

responsive and professional.

Avoid generic AI-generated layouts.

The gpt-taste skill is specifically described as a stricter GPT/Codex-oriented variant with stronger layout variation and anti-slop direction. (GitHub)

## 11. Example: Image-to-Code Workflow

If you already have a screenshot or design reference, use:

image-to-code

Install:

npx skills add https://github.com/Leonxlnx/taste-skill --skill "image-to-code"

Then provide the reference image and request:

Use the image-to-code skill.

Analyze this design reference and recreate

the interface as a responsive frontend.

Keep the visual hierarchy, spacing,

typography and overall design direction

close to the reference.

The purpose of this skill is to combine reference generation/analysis with frontend implementation. (GitHub)

## 12. Example: Redesigning an Existing Project

If you already have a website, you don't need to rebuild everything from scratch.

Use:

redesign-existing-projects

Install:

npx skills add https://github.com/Leonxlnx/taste-skill --skill "redesign-existing-projects"

Then ask:

Use the redesign-existing-projects skill.

First audit the current UI.

Identify:

- Poor spacing

- Weak visual hierarchy

- Generic layouts

- Typography problems

- Inconsistent components

- Poor responsive behavior

Then redesign the interface.

Keep the existing functionality,

but improve the overall visual quality.

The redesign skill is specifically intended to audit an existing project before making design improvements. (GitHub)

## 13. Creating a Better Design Brief

Taste Skill works better when you give the AI a clear brief.

A useful structure is:

Project:

[What are you building?]

Page:

[Landing page / dashboard / portfolio / application / etc.]

Audience:

[Who will use it?]

Purpose:

[What should the page accomplish?]

Visual style:

[Modern / premium / minimal / bold / playful / etc.]

Colors:

[Preferred colors]

Typography:

[Preferred typography or general direction]

Motion:

[None / subtle / moderate / strong]

Density:

[Spacious / balanced / dense]

References:

[Websites, screenshots, images, etc.]

Avoid:

[Things you don't want]

Example:

Project:

Expense management application.

Page:

Dashboard.

Audience:

Young professionals.

Purpose:

Allow users to quickly understand their

income, expenses and savings.

Visual style:

Premium, modern and trustworthy.

Colors:

Navy, white and green.

Motion:

Subtle.

Density:

Balanced.

Avoid:

Generic SaaS dashboard design,

excessive cards and unnecessary gradients.

## 14. How the Complete Workflow Looks

The workflow depends on which skill you choose.

### New frontend

Project

↓

design-taste-frontend

↓

AI analyzes requirements

↓

AI creates design direction

↓

AI builds frontend

↓

UI review

### Codex-focused frontend

Project

↓

gpt-taste

↓

GPT/Codex

↓

Design + implementation

↓

UI review

### Image/reference → website

Image / Reference

↓

image-to-code

↓

AI analyzes reference

↓

Design implementation

↓

Frontend

### Existing website redesign

Existing Project

↓

redesign-existing-projects

↓

UI Audit

↓

Identify Problems

↓

Redesign

↓

Improved Frontend

## 15. Git Clone and Local Repository Guide

If you want to inspect or modify the Taste Skill source code itself, clone the repository.

### Clone the repository

git clone https://github.com/Leonxlnx/taste-skill.git

Enter the directory:

cd taste-skill

Check the repository:

dir

You can inspect the available skills:

skills/

Each skill contains its own instructions, including a SKILL.md.

For example:

skills/

├── taste-skill/

│   └── SKILL.md

├── gpt-tasteskill/

│   └── SKILL.md

├── image-to-code-skill/

│   └── SKILL.md

└── redesign-skill/

└── SKILL.md

The repository is primarily a **collection of skill files**, not a normal web application that you start with:

npm run dev

You clone it when you want to inspect, customize, or develop the skills themselves. The recommended end-user installation is through npx skills add. (GitHub)

## 16. Does It Need npm install or npm run dev?

For normal usage:

**No.**

Taste Skill is not something you normally start as a local web server.

The main installation is:

npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"

Then the AI coding agent reads the installed SKILL.md. The official documentation states that no additional configuration is required for the default skill. (Taste Skill)

## 17. Updating Taste Skill

The current default design-taste-frontend is **v2 experimental**.

If you already have the older version installed, running:

npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"

updates it to the newer v2 version. (GitHub)

## 18. Using the Older v1 Version

If an existing project depends on the original v1 behavior, you can explicitly install:

npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend-v1"

The repository maintains v1 specifically for projects that need its previous behavior. (GitHub)

For new projects, the repository currently recommends the v2 default. (Taste Skill)

## 19. Important Difference Between Taste Skill and a UI Framework

Taste Skill does **not** replace:

- React

- Vue

- Svelte

- Next.js

- Tailwind CSS

- Bootstrap

- GSAP

- Other frontend technologies

Instead, it works **alongside** them.

For example:

React

+

Tailwind CSS

+

GSAP

+

Taste Skill

↓

AI-generated frontend

Taste Skill provides the **design direction and implementation rules**, while your frontend framework and libraries provide the actual technology used to build the application.

The project specifically states that its rules are framework agnostic. (GitHub)

## 20. Important Notes

- Taste Skill is an **AI-agent skill system**, not a standalone frontend framework.

- You do **not** have to install every skill.

- Each skill has a specific purpose.

- The main design-taste-frontend skill is currently **v2 experimental**. (Taste Skill)

- gpt-taste is the stricter GPT/Codex-oriented variant. (GitHub)

- image-to-code is intended for image/reference-first workflows. (GitHub)

- redesign-existing-projects is intended for improving existing projects. (GitHub)

- You can install skills with npx skills add.

- You can install the entire bundle or only a specific skill.

- You can also copy a SKILL.md into a project or paste it into an AI conversation. (GitHub)

- You normally **do not run Taste Skill with ****npm run dev**.

- The AI coding agent is what actually performs the frontend development.

- Taste Skill provides the additional design instructions that guide the agent.

## 21. Simple Summary

Taste Skill can be understood as:

YOUR IDEA

↓

Design Brief

↓

┌─────────────────┐

│   AI Agent      │

│ Codex / Claude  │

│ Cursor / etc.   │

└────────┬────────┘

↓

Taste Skill

↓

Design Rules & Analysis

↓

Layout / Typography

Spacing / Motion

Visual Hierarchy

↓

Frontend Implementation

↓

UI Review

↓

Final Website

### In one sentence

**Taste Skill is a collection of portable instructions that gives AI coding agents stronger design rules and workflows so they can create more intentional, polished, and less generic frontend interfaces.** (GitHub)
