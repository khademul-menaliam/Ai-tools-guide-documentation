# AI Website Design & Development Guide

### Using HagiCode Design, Taste Skill, Impeccable, and img2threejs

## 1. Introduction

This guide explains how beginners can use four AI-powered tools together to build a **modern, professional, and visually polished website**.

The four tools have different purposes:

- **HagiCode Design** — Helps with design direction and planning. (**Awesome DESIGN).**

- **Taste Skill** — Helps an AI coding agent make better design decisions while building the UI.

- **Impeccable** — Reviews and improves the existing UI/UX.

- **img2threejs** — Creates procedural 3D objects from reference images using Three.js.

The important thing to understand is that **these tools are not replacements for each other**.

Each tool has a different role in the development process.

## 2. What Does Each Tool Do?

## 2.1 HagiCode Design

**Website:** [https://design.hagicode.com/](https://design.hagicode.com/)

### Simple explanation

HagiCode Design is used as a **design reference and planning tool**.

It helps you think about how your website should look before you start building it.

You can use it to define things such as:

- Layout

- Colors

- Typography

- Visual style

- Components

- Spacing

- User experience

- Design direction

### Think of it as:

**"What should my website look and feel like?"**

## 2.2 Taste Skill

**Website:** [https://www.tasteskill.dev/](https://www.tasteskill.dev/)

**GitHub:** [https://github.com/Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill)

### Simple explanation

Taste Skill is an **AI coding-agent skill focused on frontend design quality**.

It helps your AI coding agent avoid generic-looking interfaces and make more intentional design decisions.

It can help with:

- Typography

- Layout

- Spacing

- Visual hierarchy

- Colors

- Motion

- Responsive design

- Component design

- Overall visual personality

### Think of it as:

**"Help the AI developer build the website with better design taste."**

## 2.3 Impeccable

**Website:** [https://impeccable.style/](https://impeccable.style/)

### Simple explanation

Impeccable is an **AI design review and improvement skill**.

After your website has been built, you can ask Impeccable to inspect the interface and identify problems.

It can help improve:

- UI/UX

- Typography

- Spacing

- Visual hierarchy

- Responsiveness

- Accessibility

- Consistency

- Design quality

### Think of it as:

**"Review my website like a professional UI/UX designer and fix the problems."**

## 2.4 img2threejs

**GitHub:** [https://github.com/img2threejs/img2threejs](https://github.com/img2threejs/img2threejs)

### Simple explanation

img2threejs is an **AI workflow for creating procedural 3D models from reference images** using Three.js and TypeScript.

It can analyze a reference image and generate code that creates the 3D object.

The generated object can be represented as a:

THREE.Group

You can then add that object to a Three.js scene and use it inside your website.

### Think of it as:

**"Turn a reference image into a programmable 3D Three.js object."**

## 3. How the Four Tools Work Together

The easiest way to understand the workflow is:

WEBSITE IDEA

↓

HagiCode Design

↓

DESIGN DIRECTION

↓

Taste Skill

↓

BUILD THE UI

↓

Need 3D Object?

↙       ↘

YES        NO

↓          ↓

img2threejs      │

↓           │

THREE.Group      │

↓           │

└─────┬─────┘

↓

Impeccable

↓

REVIEW & FIX

↓

RESPONSIVE TEST

↓

FINAL WEBSITE

## 4. Recommended Workflow for Beginners

Do **not** try to use all four tools at the same time.

Use them in stages.

### Stage 1 — Plan

Use:

**HagiCode Design**

↓

Create your design direction.

### Stage 2 — Build

Use:

**Taste Skill + AI Coding Agent**

↓

Create the website.

### Stage 3 — Add 3D

Use:

**img2threejs**

↓

Only if your website needs 3D.

### Stage 4 — Review

Use:

**Impeccable**

↓

Find problems and improve the UI.

## 5. Step-by-Step Beginner Guide

## Step 1 — Decide What You Want to Build

Start with a simple idea.

For this demo, we will create:

**A modern finance management website.**

The website will help users manage:

- Income

- Expenses

- Savings

- Financial goals

## 6. Create a Simple Project Brief

Before asking AI to write code, create a short brief.

Example:

PROJECT:

Finance Management Website

TARGET USERS:

Individuals and small businesses.

MAIN GOAL:

Help users easily understand and manage their income,

expenses, savings, and financial goals.

MAIN PAGES:

- Landing Page

- Dashboard

- Transactions

- Goals

- Reports

- Settings

BRAND:

Modern, trustworthy and premium.

COLORS:

Navy + Green.

DESIGN STYLE:

Clean, modern, professional and slightly futuristic.

AVOID:

- Generic AI dashboard designs

- Excessive rounded cards

- Too many gradients

- Excessive glassmorphism

- Unnecessary animations

- Too many colors

This brief becomes the foundation for the project.

## 7. Step 2 — Create the Design Direction

Now use **HagiCode Design** as your design reference/planning stage.

The goal is not to build the website yet.

First decide:

### Typography

For example:

Headings:

Bold, modern sans-serif

Body:

Clean and readable sans-serif

### Colors

Primary:

Navy

Accent:

Green

Background:

Light neutral

Text:

Dark neutral

### Layout

Large hero section

Clear content hierarchy

Generous whitespace

Structured sections

Responsive layout

### Visual style

Premium

Clean

Modern

Trustworthy

Minimal

Save these decisions in your project documentation, for example:

design/DESIGN.md

## 8. Step 3 — Install Taste Skill

Now prepare your AI coding agent.

Install Taste Skill:

npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"

Or for a Codex setup:

npx skills add Leonxlnx/taste-skill -a codex

After installation, open your website project in your AI coding agent.

## 9. Step 4 — Ask AI to Build the Website

Now give the AI your project brief.

Example prompt:

Build a modern finance management website.

PROJECT:

Finance Management Website

TARGET USERS:

Individuals and small businesses.

MAIN GOAL:

Help users manage income, expenses, savings and financial goals.

BRAND:

Modern, trustworthy and premium.

COLORS:

Navy + green.

DESIGN STYLE:

Clean, modern, professional and slightly futuristic.

DESIGN REQUIREMENTS:

- Strong typography

- Clear visual hierarchy

- Good spacing

- Responsive layout

- Professional data visualization

- Subtle animations

- Consistent components

AVOID:

- Generic AI-looking layouts

- Excessive rounded cards

- Random gradients

- Excessive glassmorphism

- Unnecessary animations

Build the landing page first.

Do not build all pages at once.

Taste Skill helps the AI coding agent make better frontend design decisions while implementing the website.

## 10. Step 5 — Run and Check the Website

Start your development server.

For example:

npm run dev

Open the local URL shown by the terminal.

Usually it will look similar to:

http://localhost:5173

Now visually check the website.

Ask yourself:

- Does the page look professional?

- Is the important information easy to find?

- Does the spacing look good?

- Are the fonts readable?

- Does it work on mobile?

- Does anything look unnecessary?

- Does it look too much like a generic AI template?

## 11. Step 6 — Add a 3D Element

This step is **optional**.

Only use img2threejs if your website benefits from a 3D element.

For example, you might want a 3D financial object in the hero section.

Find a suitable reference image.

Example:

Reference Image

↓

3D financial object

↓

Hero section

Clone img2threejs:

gh repo clone img2threejs/img2threejs

Then configure it as a skill for your AI coding agent according to its repository instructions.

Give the agent your reference image and a prompt such as:

Rebuild this object as a procedural Three.js model.

Keep the proportions, shape, colors and important details

as close to the reference image as possible.

The final object should be usable as a THREE.Group

inside a Three.js website.

The generated result can then be integrated into your Three.js scene.

## 12. Understanding the THREE.Group

Suppose img2threejs creates a 3D object consisting of:

THREE.Group

│

├── Main Body

├── Detail 1

├── Detail 2

├── Detail 3

└── Detail 4

You can add the entire object to your scene:

const model = createModel();

scene.add(model);

You can then manipulate the whole object:

model.rotation.y += 0.01;

Or:

model.scale.set(2, 2, 2);

This allows the generated model to become an interactive part of your website.

## 13. Step 7 — Review the Website with Impeccable

Once the first version is complete, use Impeccable.

Install it in your project:

npx impeccable install

Then initialize it:

/impeccable init

Now ask it to review your page.

Example:

/impeccable

Review this website as a professional UI/UX designer.

Check:

- Visual hierarchy

- Typography

- Spacing

- Layout

- Color usage

- Component consistency

- Responsive design

- Accessibility

- User experience

- Unnecessary visual elements

Identify the most important problems and fix them.

Do not change the application's core functionality.

## 14. Step 8 — Improve Specific Problems

You can also ask Impeccable to solve individual problems.

### Typography

/impeccable

Improve the typography hierarchy on this page.

Make headings, labels and body text easier to distinguish.

### Spacing

/impeccable

Review the spacing and alignment throughout this page.

Fix inconsistent padding, margins and gaps.

### Mobile

/impeccable

Improve the mobile version of this page.

Make sure the layout works properly on small screens.

### Visual hierarchy

/impeccable

Improve the visual hierarchy.

Make the most important information immediately noticeable.

## 15. Step 9 — Final Testing

After the design improvements, test the website again.

### Desktop

Check:

1920px

1440px

1280px

### Tablet

Check:

1024px

768px

### Mobile

Check:

430px

390px

375px

Check:

- Navigation

- Buttons

- Text

- Images

- Cards

- Tables

- Charts

- 3D objects

- Animations

- Page scrolling

## 16. Complete Demo Workflow

Here is the complete example from beginning to end.

## Demo Project

**Project:** Finance Management Website

### Step 1 — Idea

I want to build a modern finance management website.

↓

### Step 2 — Planning

Use **HagiCode Design**

Define:

Colors

Typography

Layout

Visual style

Spacing

Components

Animations

↓

### Step 3 — Install Taste Skill

npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"

↓

### Step 4 — Build

Give the AI coding agent:

Build the finance website using the design direction

defined in DESIGN.md.

Make it premium, modern and trustworthy.

Avoid generic AI-generated design patterns.

↓

### Step 5 — Run

npm run dev

↓

### Step 6 — Add 3D

If required:

Reference Image

↓

img2threejs

↓

THREE.Group

↓

Three.js application

↓

### Step 7 — Review

Install:

npx impeccable install

Initialize:

/impeccable init

Then:

/impeccable

Review and improve the current website.

Focus on visual hierarchy, typography,

spacing, responsiveness and consistency.

↓

### Step 8 — Test

Desktop

↓

Tablet

↓

Mobile

↓

### Step 9 — Final Polish

Check:

✓ UI consistency

✓ Typography

✓ Colors

✓ Spacing

✓ Responsive design

✓ Accessibility

✓ Animations

✓ 3D performance

✓ Loading performance

✓ User experience

↓

### Final Result

PROFESSIONAL WEBSITE

│

┌─────────────┼─────────────┐

↓             ↓             ↓

UI/UX          3D Assets     Responsive

│             │             │

Taste Skill    img2threejs    Impeccable

│             │             │

└─────────────┼─────────────┘

↓

Final Website

## 17. Best Practices

## Use the tools for their specific purpose

Don't ask every tool to do everything.

### HagiCode

Use for:

**Design planning and visual direction**

### Taste Skill

Use for:

**Design-aware frontend implementation**

### img2threejs

Use for:

**Procedural 3D assets**

### Impeccable

Use for:

**UI/UX review and refinement**

## 18. Recommended Beginner Workflow

If you are completely new, start with only:

HagiCode

↓

Taste Skill

↓

Build website

↓

Impeccable

Once you're comfortable, add:

img2threejs

when you need 3D.

You **do not need to use img2threejs on every website**.

## 19. Final Summary

These four tools can work together as a complete AI-assisted website development workflow.

### HagiCode Design

**Plan the design.**

"What should the website look and feel like?"

### Taste Skill

**Build the design properly.**

"Help the AI create a distinctive and high-quality interface."

### img2threejs

**Create 3D assets.**

"Turn a reference image into a programmable Three.js 3D object."

### Impeccable

**Review and polish the result.**

"Find what's wrong with the UI and improve it."

### The complete process

IDEA

↓

DESIGN PLAN

↓

HagiCode

↓

DESIGN SYSTEM

↓

Taste Skill

↓

FRONTEND DEVELOPMENT

↓

img2threejs (optional)

↓

3D INTEGRATION

↓

Impeccable

↓

UI/UX REVIEW

↓

RESPONSIVE TESTING

↓

FINAL POLISH

↓

PRODUCTION

**The goal is not to use as many AI tools as possible. The goal is to give each tool a clear responsibility and use them together in the correct order.**
