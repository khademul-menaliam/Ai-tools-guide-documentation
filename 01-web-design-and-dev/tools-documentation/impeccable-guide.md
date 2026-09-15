# Impeccable — Documentation Guide

## 1. Overview

**Impeccable** is an open-source AI design skill that helps AI coding agents create, improve, and review **web interfaces and UI/UX designs**.

It works alongside AI coding tools such as **Codex, Claude Code, Cursor, GitHub Copilot, Gemini CLI, and OpenCode**.

The main purpose of Impeccable is to help AI-generated interfaces look more **professional, intentional, consistent, and user-friendly**, instead of producing generic or repetitive AI-style designs.

### Main idea

**Existing/New UI → AI Design Analysis → Design Improvements → Code Changes → Better UI**

Impeccable provides a collection of design-focused commands that an AI coding agent can use to improve a frontend project.

Official website:

https://impeccable.style/

## 2. What It Is, How It Works & How to Use It

## What is Impeccable?

Impeccable is an **AI design skill for coding agents**.

It does not work like a traditional design application such as Figma where you manually drag and drop UI elements.

Instead, you give instructions to your AI coding agent, and Impeccable provides the agent with **design knowledge, guidelines, commands, and workflows** that help it make better UI decisions.

For example, you can ask the agent to:

- Improve a dashboard

- Fix poor spacing

- Improve typography

- Make the layout more responsive

- Improve visual hierarchy

- Make a page look more professional

- Review an existing design

- Remove generic AI-generated design patterns

- Create a design system

- Improve consistency across pages

## How Does It Work?

The general workflow is:

Your Web Project

↓

AI Coding Agent

↓

Impeccable Design Skill

↓

Analyze Existing UI

↓

Understand Design Requirements

↓

Apply Design Improvements

↓

Modify Frontend Code

↓

Review the Result

Impeccable does not replace your coding agent.

Instead, it **gives the coding agent additional design expertise and structured commands**.

## Step 1 — Install Impeccable

Impeccable can be installed into your project using:

npx impeccable install

This installs the Impeccable skill files that your AI coding agent can use.

## Step 2 — Initialize Impeccable

Open your project with your supported AI coding agent.

Then run:

/impeccable init

This initializes Impeccable for the project.

The initialization process can help the agent understand the project's existing design and establish the foundation for future design work.

## Step 3 — Analyze Your Project

After installation, you can ask Impeccable to review your existing UI.

For example:

/impeccable audit

Review the current dashboard design.

Identify problems with spacing, typography,

visual hierarchy, responsiveness, and consistency.

The agent can then inspect the project and identify areas that need improvement.

## Step 4 — Ask for Design Improvements

You can give Impeccable a specific design task.

Example:

/impeccable

Improve the dashboard UI.

Make the visual hierarchy clearer,

improve spacing and typography,

and make the interface feel more professional.

Keep the existing brand colors.

The AI agent will analyze the existing frontend code and make appropriate changes.

# Common Uses

## Improve an Existing UI

If your current UI looks basic:

/impeccable

Improve this page so it looks more polished and professional.

Keep the existing functionality and content.

## Improve Typography

/impeccable

Improve the typography hierarchy on this page.

Make headings, labels, body text, and supporting information

easier to distinguish.

## Improve Spacing

/impeccable

Review the spacing throughout this page.

Fix inconsistent padding, margins, gaps, and alignment.

## Improve Responsive Design

/impeccable

Make this page responsive for desktop, tablet, and mobile.

Keep the existing functionality while improving the layout

at different screen sizes.

## Improve Visual Hierarchy

/impeccable

Improve the visual hierarchy of this dashboard.

Make the most important information immediately noticeable

and reduce visual competition between less important elements.

## Create a Design System

Impeccable can also help establish consistent design rules for a project.

For example:

/impeccable

Create a consistent design system for this application.

Define typography, spacing, colors, components, borders,

shadows, and other important visual rules.

This helps different pages of the application maintain a consistent visual language.

## 3. Git Clone, Run Guide & Usage Guide

## Important: Impeccable Does Not Need to Be Run Like a Normal Web App

Unlike a typical frontend project, you generally **do not clone Impeccable and run ****npm run dev**** to open Impeccable in a browser**.

Impeccable is installed as a **skill for an AI coding agent**.

The recommended installation is:

npx impeccable install

Therefore, the normal workflow is:

Your Project

↓

Install Impeccable

↓

AI Coding Agent

↓

Use Impeccable commands

↓

AI modifies your project

# Option 1 — Recommended Installation

Open your existing frontend project:

cd C:\path\to\your-project

Then run:

npx impeccable install

After installation, open the project using your supported AI coding agent.

Then initialize:

/impeccable init

You can now start using the Impeccable commands.

# Option 2 — Clone the GitHub Repository

If you want to inspect the source code or documentation, you can clone the repository.

Example:

git clone https://github.com/pbakaus/impeccable.git

Then:

cd impeccable

You can inspect the repository files and documentation.

**Note:** Cloning the repository is mainly useful for developers who want to inspect or contribute to Impeccable. It is not normally required just to use the skill in a project.

For normal usage, prefer:

npx impeccable install

# Basic Usage Guide

Once Impeccable is installed, you work inside your frontend project through your AI coding agent.

### Example 1 — Start a design task

/impeccable

Review this page and improve the overall UI/UX.

Make it modern, clean, professional, and easy to use.

Do not change the application's functionality.

### Example 2 — Improve an existing dashboard

/impeccable

Improve this dashboard.

Focus on:

- Visual hierarchy

- Typography

- Spacing

- Card design

- Navigation

- Responsive behavior

Keep the existing functionality and data.

### Example 3 — Make an AI-generated UI less generic

/impeccable

This interface looks too generic and AI-generated.

Redesign the visual presentation to make it feel more

intentional and professional.

Keep the existing functionality.

### Example 4 — Review before release

/impeccable

Review this UI before production release.

Find design inconsistencies, accessibility problems,

responsive issues, poor spacing, typography problems,

and other visual issues.

Suggest and apply appropriate improvements.

# Understanding the Workflow

A practical workflow for a project can be:

## 1. Create or open your frontend project

↓

## 2. Install Impeccable

↓

## 3. Run /impeccable init

↓

## 4. Ask the AI agent to review the project

↓

## 5. Identify UI/UX problems

↓

## 6. Give specific improvement instructions

↓

## 7. AI modifies the frontend code

↓

## 8. Run the application

↓

## 9. Review the visual result

↓

## 10. Ask for additional improvements

↓

## 11. Final UI

# Example: Complete Real-World Usage

Suppose you have a **finance dashboard** with:

- Income

- Expenses

- Balance

- Charts

- Recent transactions

Your current dashboard works, but the UI looks basic.

You can ask:

/impeccable

Review and improve this finance dashboard.

Goals:

- Make the balance and important financial information

immediately visible.

- Improve the visual hierarchy.

- Make cards cleaner and more consistent.

- Improve typography and spacing.

- Make charts easier to understand.

- Improve mobile responsiveness.

- Keep the existing functionality and data.

- Keep the existing brand identity.

The AI agent can then inspect the existing code and implement the design improvements.

# img2threejs vs Impeccable

These two tools have very different purposes.

| Tool | Main Purpose |
| --- | --- |
| img2threejs | Convert a reference image into a procedural Three.js 3D model |
| Impeccable | Improve and create professional web UI/UX through an AI coding agent |

### img2threejs

Image

↓

AI

↓

Three.js / TypeScript

↓

THREE.Group

↓

3D Model

### Impeccable

Web Project

↓

AI Coding Agent

↓

Design Analysis

↓

UI/UX Improvements

↓

Frontend Code

↓

Better Interface

# Important Notes

- Impeccable is primarily an **AI coding-agent skill**, not a standalone design application.

- It works with supported AI coding agents.

- You generally do not need to clone the repository to use it.

- The recommended installation method is:

npx impeccable install

- After installation, initialize it with:

/impeccable init

- You can then give the AI agent design-related instructions.

- Impeccable can work with an existing frontend rather than requiring you to start a project from scratch.

- It is particularly useful when an AI-generated UI works technically but needs better **visual design, consistency, hierarchy, responsiveness, and polish**.

**Official website:** [https://impeccable.style/](https://impeccable.style/)

**Official GitHub repository:** [https://github.com/pbakaus/impeccable](https://github.com/pbakaus/impeccable)
