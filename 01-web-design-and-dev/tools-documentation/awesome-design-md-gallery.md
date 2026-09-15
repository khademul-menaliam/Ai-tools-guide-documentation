# Awesome Design MD Gallery — Documentation Guide

## 1. Overview

**Awesome Design MD Gallery** is a design-system gallery available at:

https://design.hagicode.com/

It provides a collection of design references from well-known websites and products. The gallery currently contains dozens of design entries that can be searched, previewed, and inspected.

Each design includes a **DESIGN.md** file that describes the visual language of that design, including colors, typography, components, spacing, layout, responsive behavior, and design rules. (Awesome Design MD Gallery)

The main purpose is to help developers use **AI coding agents** to create UI that follows a consistent design style.

### Basic idea

Browse Design

↓

Choose a Design

↓

Preview the Design

↓

Open DESIGN.md

↓

Copy DESIGN.md to your project

↓

Tell your AI coding agent to use it

↓

AI generates UI following the design system

## 2. What It Is, How It Works & How to Use It

## What is Awesome Design MD?

Awesome Design MD is a collection of **design-system documentation in Markdown format**.

The important file is:

DESIGN.md

A DESIGN.md file acts as a set of instructions that tells an AI coding agent **how the interface should look and feel**.

It is not a CSS framework and it does not automatically change your website.

Instead, it gives the AI agent detailed design instructions that the agent can use while creating or modifying your UI. (GitHub)

## What Does DESIGN.md Contain?

A typical design document can contain information about:

## 1. Visual Theme & Atmosphere

Describes the overall visual direction.

For example:

- Minimal

- Professional

- Modern

- Dense

- Spacious

- Editorial

## 2. Color Palette

Defines colors and their purposes.

Example:

Primary: #635BFF

Background: #FFFFFF

Text: #1A1A1A

Border: #E5E7EB

## 3. Typography

Defines:

- Font family

- Font sizes

- Font weights

- Heading styles

- Body text

- Line heights

## 4. Component Styling

Describes how common UI components should look.

For example:

- Buttons

- Cards

- Forms

- Inputs

- Navigation

- Tables

- Modals

## 5. Layout Principles

Defines:

- Spacing

- Grid

- Container widths

- Alignment

- Padding

- Whitespace

## 6. Depth & Elevation

Describes:

- Shadows

- Borders

- Surface hierarchy

- Layering

## 7. Do's and Don'ts

Provides rules about what should and should not be done when implementing the design.

## 8. Responsive Behavior

Describes how the design should behave on:

- Desktop

- Tablet

- Mobile

## 9. Agent Prompt Guide

Can provide instructions and prompts that can be given directly to an AI coding agent. (GitHub)

# How Does It Work?

The gallery itself is mainly a **visual browsing and documentation tool**.

The workflow is:

Awesome Design MD Gallery

↓

Browse available designs

↓

Select a design

↓

View live preview

↓

Read DESIGN.md

↓

Copy/download DESIGN.md

↓

Your own project

↓

AI Coding Agent reads it

↓

AI creates the UI

For example, you can select a Stripe-inspired design and use its DESIGN.md as a reference when asking an AI agent to create a dashboard.

The gallery provides a **live preview**, documentation, and an option to copy/download the DESIGN.md for individual entries. (Awesome Design MD Gallery)

# How to Use It

## Step 1 — Open the Gallery

Open:

Awesome Design MD Gallery

You will see a collection of design styles.

The gallery currently provides search functionality and dozens of design entries. (Awesome Design MD Gallery)

## Step 2 — Search for a Design

Use the search box to find a specific design.

For example:

Stripe

Apple

Linear

Notion

Vercel

Spotify

Supabase

Figma

Tesla

You can also search using general keywords such as:

fintech

dashboard

minimal

gradient

documentation

## Step 3 — Open a Design

Click the design you are interested in.

The detail page provides:

- Design name

- Design description

- Live preview

- Light mode preview

- Dark mode preview

- README

- DESIGN.md

The gallery allows you to inspect the design before deciding whether to use it. (Awesome Design MD Gallery)

## Step 4 — Review the Design

Check the visual preview first.

Look at:

- Colors

- Typography

- Buttons

- Cards

- Spacing

- Navigation

- Layout

- Dark/light appearance

This helps you decide whether the design is appropriate for your project.

## Step 5 — Open DESIGN.md

Open the **DESIGN** section.

This contains the actual design instructions.

You can read the document and understand the rules that an AI coding agent can follow.

## Step 6 — Copy or Download DESIGN.md

Use the **Copy DESIGN.md** or download option provided by the gallery.

Place the file inside your project.

For example:

my-project/

├── src/

├── public/

├── package.json

└── DESIGN.md

## Step 7 — Tell Your AI Agent to Use It

Now open your AI coding agent, such as:

- Codex

- Claude Code

- Cursor

- Other compatible AI coding agents

Give it an instruction such as:

Use the DESIGN.md file in this project as the design system.

Build the dashboard page according to the colors,

typography, spacing, components, layout, and

responsive rules defined in DESIGN.md.

The AI can then use the document as the design reference while generating the UI.

## 3. Git Clone, Run Guide & Usage Guide

There are two different repositories involved:

- The **upstream collection** containing the actual DESIGN.md files.

- The **HagiCode gallery website** that provides a visual way to browse them.

The HagiCode gallery source is available as:

**HagiCode-org/awesome-design-md-site**

It is an **Astro-based gallery site** for browsing, previewing, and documenting entries from the Awesome Design MD collection. (GitHub)

# Clone the HagiCode Gallery

First, clone the repository:

git clone https://github.com/HagiCode-org/awesome-design-md-site.git

Move into the project:

cd awesome-design-md-site

# Install Dependencies

This is an Astro/TypeScript project, so first install the required dependencies.

Check the project's package.json to determine the package manager and required commands.

For an npm-based setup, use:

npm install

# Run the Project Locally

Start the development server:

npm run dev

The terminal will provide a local URL, typically similar to:

http://localhost:4321

Open that address in your browser.

You should then see the **Awesome Design MD Gallery running locally**.

Always follow the repository's current README.md and package.json instructions if the commands or requirements change.

# How to Use the Local Gallery

Once the project is running locally:

### Step 1

Open the local URL in your browser.

### Step 2

Browse the available design entries.

### Step 3

Select a design.

### Step 4

Preview the design in light and dark mode.

### Step 5

Read the README and DESIGN.md.

### Step 6

Copy the desired DESIGN.md.

### Step 7

Place it inside your own project.

Example:

my-project/

├── src/

├── public/

├── package.json

└── DESIGN.md

### Step 8

Ask your AI coding agent to use the file.

Example:

Use the DESIGN.md file as the design system for this project.

Create a modern dashboard using the design rules

defined in DESIGN.md.

Follow the specified:

- colors

- typography

- spacing

- components

- layout

- responsive behavior

- visual style

# Example Use Case

Suppose you are building a finance dashboard.

You could:

## 1. Open design.hagicode.com

↓

## 2. Search for "Stripe" or another suitable style

↓

## 3. Preview the design

↓

## 4. Open DESIGN.md

↓

## 5. Copy DESIGN.md

↓

## 6. Add it to your finance project

↓

## 7. Open Codex / Claude Code / Cursor

↓

## 8. Ask the AI to build the dashboard using DESIGN.md

↓

## 9. Review the generated UI

↓

## 10. Make adjustments if necessary

# Important Notes

- **Awesome Design MD Gallery is primarily a design reference and documentation gallery.**

- It does **not automatically redesign your website**.

- DESIGN.md is the important reusable asset.

- The file is written in plain Markdown, so it does not require a special design-file format or Figma export. (GitHub)

- The gallery's entries are **inspired/extracted from publicly visible websites**, and the individual pages explicitly note that they are **not the official design systems** of those companies. Colors, fonts, and spacing may not be completely accurate. (Awesome Design MD Gallery)

- You should treat the files as **design references**, not as official brand guidelines.

- The main benefit is giving an AI coding agent a structured visual reference instead of relying only on a vague prompt.

# Complete Workflow Summary

┌──────────────────────────────┐

│  Awesome Design MD Gallery   │

└──────────────┬───────────────┘

↓

Browse Design Styles

↓

Select a Design

↓

Preview the Design

↓

Read DESIGN.md

↓

Copy DESIGN.md

↓

Add to Your Project

↓

AI Coding Agent Reads It

↓

AI Builds Your Interface

↓

Review & Improve UI

### In one sentence

**Awesome Design MD Gallery provides reusable ****DESIGN.md**** design specifications and visual previews that developers can use as a structured design reference for AI coding agents when building consistent web interfaces.**
