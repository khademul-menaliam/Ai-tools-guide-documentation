# img2threejs — Documentation Guide

## 1. Overview

**img2threejs** is an open-source AI-powered workflow for recreating objects from reference images as **procedural 3D models using Three.js and TypeScript**.

Instead of manually creating a 3D model, the system uses an AI coding agent to analyze a reference image and generate code that builds the object using Three.js.

The generated model is usually returned as a **THREE.Group**, which can then be added to a Three.js scene and used in a web application.

### Main idea

**Reference Image → AI Analysis → Three.js Code → THREE.Group → Interactive 3D Model**

Repository:

https://github.com/img2threejs/img2threejs

## 2. What It Is, How It Works & How to Use It

## What is img2threejs?

img2threejs is a **skill/workflow for AI coding agents**, rather than a traditional website where users upload an image and click a "Generate 3D" button.

It is designed to work with AI coding agents such as:

- Codex

- Claude Code

- OpenCode

The AI agent uses the reference image and the img2threejs workflow to create a procedural Three.js model.

The result is **code**, rather than simply a static 3D file.

## How Does It Work?

The general workflow is:

Reference Image

↓

AI analyzes the image

↓

Creates a 3D specification

↓

Generates Three.js / TypeScript code

↓

Creates a THREE.Group

↓

Renders and checks the result

↓

Final procedural 3D model

### Step 1 — Provide a Reference Image

Provide an image of the object that you want to recreate.

For example:

- Chair

- Headphones

- Car

- Bottle

- Character

- Product

- Furniture

A clear image with a simple background generally makes the object easier to analyze.

### Step 2 — AI Analyzes the Image

The AI examines the reference image and identifies things such as:

- Overall shape

- Dimensions and proportions

- Individual components

- Colors

- Materials

- Position of components

- Details and decorations

### Step 3 — Create a 3D Specification

The information from the image is converted into a structured plan for the 3D model.

For example, a chair could be broken down into:

Chair

├── Seat

├── Backrest

├── Front Left Leg

├── Front Right Leg

├── Back Left Leg

└── Back Right Leg

### Step 4 — Generate Three.js Code

The AI then creates TypeScript/Three.js code to construct the model.

The model can contain multiple meshes and components grouped together.

### Step 5 — Create a THREE.Group

The generated parts are usually placed inside a THREE.Group.

Example:

const model = new THREE.Group();

model.add(body);

model.add(wheel1);

model.add(wheel2);

return model;

The complete object can then be added to a Three.js scene:

scene.add(model);

### Step 6 — Review and Improve

The model can be rendered and compared with the reference image.

If something doesn't look correct, the AI can modify the geometry, materials, proportions, or other details and generate an improved version.

## 3. Git Clone, Run Guide & Usage Guide

## Prerequisites

Before starting, make sure you have:

- Git

- GitHub CLI (gh)

- Python 3.10 or newer

- An AI coding agent such as Codex, Claude Code, or OpenCode

Check Git:

git --version

Check GitHub CLI:

gh --version

Check Python:

python --version

Python should be **3.10 or newer**.

## Step 1 — Clone the Repository

Open PowerShell or Terminal and run:

gh repo clone img2threejs/img2threejs

Move into the repository:

cd img2threejs

Check the files:

dir

The repository contains the img2threejs skill, documentation, scripts, and supporting files.

## Step 2 — Install / Configure the Skill

img2threejs is intended to be used as a **skill for an AI coding agent**.

For Codex, the skill can be placed under:

~/.codex/skills/img2threejs

For Claude Code:

~/.claude/skills/img2threejs

The exact setup can depend on the AI coding agent being used, so follow the repository's current README.md and SKILL.md instructions.

## Step 3 — Prepare a Reference Image

Choose a clear image of the object you want to recreate.

Example:

C:\Users\YourName\Pictures\headphones.jpg

For the first test, use a relatively simple object.

### Recommended

- Clear object

- Good lighting

- Minimal background

- Object clearly visible

- Preferably one main object

### Avoid for the first test

- Very complicated scenes

- Many objects

- Extremely low-resolution images

- Objects that are mostly hidden

- Images with heavy visual effects

# Using img2threejs

Once the skill is available to your AI coding agent, provide the reference image and request a 3D reconstruction.

Example prompt:

Rebuild this object as a procedural Three.js model.

Use the provided image as the reference.

Keep the proportions, shape, details, materials, colors,

and overall appearance as close to the reference as possible.

You can also be more specific:

Recreate this object as a procedural Three.js model.

The final result should be a THREE.Group.

Break the object into logical components.

Use appropriate Three.js geometry and materials.

Keep the proportions and visual appearance close to the reference image.

The AI agent will then follow the img2threejs workflow to analyze the image and generate the model.

# Understanding the Generated THREE.Group

A THREE.Group is a container that holds multiple Three.js objects.

For example, a bicycle could be structured like:

THREE.Group

├── Frame

├── Front Wheel

├── Rear Wheel

├── Handlebar

├── Seat

└── Pedals

The entire bicycle can be controlled as one object.

Example:

const bicycle = createBicycle();

scene.add(bicycle);

You can move it:

bicycle.position.set(0, 0, 0);

Rotate it:

bicycle.rotation.y = Math.PI / 4;

Scale it:

bicycle.scale.set(2, 2, 2);

This makes the generated model easy to integrate into an existing Three.js application.

# Using the Model in a Three.js Project

After the model has been generated, it can be placed inside a normal Three.js project.

Example project structure:

my-three-project/

├── index.html

├── package.json

└── src/

├── main.ts

└── model.ts

The generated model can be placed in:

src/model.ts

Then import it into your Three.js application:

import { createModel } from "./model";

const model = createModel();

scene.add(model);

After starting the application, the generated object can be displayed as an interactive 3D model.

# Complete Workflow

The complete process can be summarized as:

## 1. Clone img2threejs

↓

## 2. Configure it for your AI coding agent

↓

## 3. Prepare a reference image

↓

## 4. Give the image to the AI agent

↓

## 5. Request a procedural 3D reconstruction

↓

## 6. AI analyzes the image

↓

## 7. AI generates Three.js / TypeScript code

↓

## 8. Code creates a THREE.Group

↓

## 9. Render and review the model

↓

## 10. Improve the model if necessary

↓

## 11. Use the THREE.Group in your Three.js application

# Important Notes

- **img2threejs is not simply an image-upload website.**

- It is primarily an **AI-agent skill/workflow**.

- The main output is **procedural Three.js/TypeScript code**.

- The generated model can be represented as a **THREE.Group**.

- You can use that group inside your own Three.js application.

- The quality of the result depends on the reference image and the AI agent's ability to interpret it.

- For the latest installation and usage instructions, always check the repository's README.md and SKILL.md.

**Official repository:** [https://github.com/img2threejs/img2threejs](https://github.com/img2threejs/img2threejs)
