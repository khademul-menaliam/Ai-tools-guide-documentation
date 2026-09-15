# Awesome DESIGN.md — Documentation Guide

## 1. Overview

**Awesome DESIGN.md** is an open-source collection of ready-to-use DESIGN.md files based on the visual design patterns of popular websites and products.

The purpose is to help **AI coding agents generate UI that follows a specific visual design style**.

Instead of explaining colors, typography, spacing, buttons, cards, layouts, and responsive behavior to an AI agent manually, you can provide a DESIGN.md file containing those design rules. (GitHub)

### Main idea

DESIGN.md

↓

AI coding agent reads design rules

↓

AI builds the UI

↓

UI follows the selected design language

The repository contains design references for many products and brands, including Vercel, Linear, Stripe, Notion, Supabase, Figma, Apple, Airbnb, Shopify, Spotify, BMW, Tesla, and many others. (GitHub)

Repository:

https://github.com/VoltAgent/awesome-design-md

## 2. What It Is, How It Works & How to Use It

## What is DESIGN.md?

DESIGN.md is a normal **Markdown file** that describes how a project should **look and feel**.

It is intended to be read by AI coding/design agents.

For example, a DESIGN.md might define:

- Color palette

- Typography

- Font sizes

- Buttons

- Cards

- Forms

- Navigation

- Spacing

- Layout

- Shadows

- Responsive behavior

- Design rules

- Do's and Don'ts

The repository describes the difference between these two types of instructions:

AGENTS.md

↓

How the project should be built

DESIGN.md

↓

How the project should look and feel

(GitHub)

## How Does It Work?

The basic workflow is:

Choose a DESIGN.md

↓

Copy it into your project

↓

AI coding agent reads it

↓

Ask the AI to build a page

↓

AI follows the design rules

↓

Generated UI matches the selected design style

### Example

Suppose you want to build a landing page inspired by a particular design style.

You can take the corresponding DESIGN.md and place it in your project:

my-project/

├── src/

├── package.json

└── DESIGN.md

Then tell your AI coding agent:

Build a modern landing page.

Follow the design rules defined in DESIGN.md.

Make sure the colors, typography, spacing,

components, and responsive behavior follow the design system.

The AI reads DESIGN.md and uses those instructions while generating the UI.

## What Is Inside a DESIGN.md?

The repository's files generally contain sections covering areas such as:

## 1. Visual Theme & Atmosphere

Defines the overall mood and visual direction.

Example:

Minimal

Professional

Clean

Modern

High whitespace

## 2. Color Palette & Roles

Defines colors and what they are used for.

Example:

Primary: #000000

Background: #FFFFFF

Text: #111111

Accent: #635BFF

## 3. Typography Rules

Defines:

- Font family

- Font sizes

- Font weights

- Heading styles

- Body text

- Line heights

## 4. Component Styling

Defines how components should look:

- Buttons

- Cards

- Inputs

- Navigation

- Forms

- Links

- States such as hover and active

## 5. Layout Principles

Defines:

- Spacing

- Grid

- Alignment

- Container sizes

- Whitespace

## 6. Depth & Elevation

Defines:

- Shadows

- Borders

- Surface hierarchy

- Elevation

## 7. Do's and Don'ts

Provides rules that help the AI avoid unwanted design patterns.

## 8. Responsive Behavior

Defines how the UI should behave on:

- Desktop

- Tablet

- Mobile

## 9. Agent Prompt Guide

Provides useful information and prompts for asking an AI agent to follow the design system.

(GitHub)

## 3. Git Clone, Run Guide & Usage Guide

## Important: Does It Need to Be Run?

**No.**

This repository is primarily a **collection of Markdown design documents**, not a web application that you need to start with npm run dev.

You mainly:

- Clone the repository.

- Choose a design.

- Copy its DESIGN.md.

- Put it into your project.

- Tell your AI coding agent to follow it.

The repository also provides preview.html and preview-dark.html files for visually reviewing the design information. (GitHub)

## Prerequisites

You only need Git or GitHub CLI to download the repository.

Check Git:

git --version

Or check GitHub CLI:

gh --version

## Step 1 — Clone the Repository

Using Git:

git clone https://github.com/VoltAgent/awesome-design-md.git

Then:

cd awesome-design-md

Or using GitHub CLI:

gh repo clone VoltAgent/awesome-design-md

cd awesome-design-md

## Step 2 — Explore the Repository

Check the files:

dir

You will find a structure similar to:

awesome-design-md/

├── design-md/

├── README.md

├── LICENSE

└── CONTRIBUTING.md

The design-md directory contains the available design references. (GitHub)

## Step 3 — Choose a Design

Open the design-md directory and choose the design you want.

For example, you may find designs for:

Vercel

Linear

Stripe

Notion

Supabase

Figma

Apple

Airbnb

Shopify

Spotify

Tesla

BMW

...

The repository contains many categories and design references. (GitHub)

## Step 4 — Copy the DESIGN.md

Suppose you choose a particular design.

Copy its:

DESIGN.md

into the root of your own project.

For example:

my-project/

├── src/

├── public/

├── package.json

└── DESIGN.md

You do **not** need to copy the entire Awesome DESIGN.md repository into your application.

Usually, you only need the DESIGN.md that you want to use.

# Using DESIGN.md With an AI Coding Agent

Once the file is inside your project, open your project using your AI coding agent.

Then give the agent an instruction such as:

Read DESIGN.md before making any UI changes.

Build a modern dashboard page following

all design rules defined in DESIGN.md.

Follow the defined colors, typography,

spacing, components, layout, responsive behavior,

and design principles.

The AI can then use the document as the visual design reference.

# Example Project Workflow

awesome-design-md

↓

Choose a DESIGN.md

↓

Copy DESIGN.md

↓

Your Project

↓

AI Coding Agent

↓

Read DESIGN.md

↓

Build UI

↓

UI follows the design system

# Example

Suppose you are building an admin dashboard.

Your project might look like:

expense-dashboard/

├── src/

│   ├── components/

│   ├── pages/

│   └── styles/

├── public/

├── package.json

└── DESIGN.md

Then tell your AI agent:

Create an expense dashboard.

Use DESIGN.md as the primary visual design reference.

Create:

- Dashboard sidebar

- Top navigation

- Income summary

- Expense summary

- Recent transactions

- Monthly spending chart

- Responsive mobile layout

Follow all typography, colors, spacing,

component styling, and responsive rules

defined in DESIGN.md.

The AI will use the DESIGN.md as its design instruction while generating the interface.

# Previewing a Design

Each design may also contain:

preview.html

preview-dark.html

These files provide a visual catalog of things such as:

- Color swatches

- Typography

- Buttons

- Cards

- Other design elements

You can open the HTML file directly in a browser to inspect the design reference. (GitHub)

# Complete Workflow

## 1. Clone awesome-design-md

↓

## 2. Open the design-md folder

↓

## 3. Choose a design

↓

## 4. Review its DESIGN.md

↓

## 5. Review preview.html if available

↓

## 6. Copy DESIGN.md into your project

↓

## 7. Open the project with your AI coding agent

↓

## 8. Tell the agent to follow DESIGN.md

↓

## 9. Ask the agent to build your UI

↓

## 10. Review the generated UI

↓

## 11. Make adjustments if necessary

# Important Notes

- **Awesome DESIGN.md is not a UI framework.**

- It does not provide React/Vue components or a CSS library.

- It is a collection of **design instruction documents**.

- DESIGN.md tells an AI agent **how the UI should look and feel**.

- You normally **do not need to run the repository**.

- You can copy an individual DESIGN.md into your own project.

- The AI coding agent can then use that file as a design reference.

- The repository is released under the **MIT License**. (GitHub)

**Official repository:** VoltAgent/awesome-design-md on GitHub

**Want a Live demo for the design & color: https://design.hagicode.com/**
