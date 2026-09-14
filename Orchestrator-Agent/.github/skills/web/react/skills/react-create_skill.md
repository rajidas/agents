---
name: react-create_skill
description: Create a new React JS app with TypeScript, Vite, Atomic Design structure, and routing
---

# React App Creation Skill

This skill generates complete React JS projects with TypeScript and Vite.

## What You Get

✅ Vite + React + TypeScript project scaffold
✅ Atomic Design folder structure (atoms, molecules, organisms, templates, pages)
✅ React Router v6 with Home / Login / Register routes
✅ Global theme with CSS variables or Tailwind CSS
✅ Authentication screens (Login, Signup)
✅ Dashboard with navigation
✅ Reusable component library with usage examples
✅ Environment variable setup (.env files)
✅ ESLint + Prettier configuration
✅ Zero setup required — runs immediately with `npm install && npm run dev`

## Capabilities

- Generate complete React project scaffold using Vite
- Atomic Design architecture implementation
- React Router v6 navigation setup
- Reusable component library (Button, Input, Card, Modal, etc.)
- Authentication flow (Login / Register / Protected Route)
- Global state wiring (optional Redux Toolkit)
- Theme and design token setup

## Requirements

- Node.js 18+ installed
- npm or yarn available

## Output

Complete, runnable React JS project ready to open in VS Code and launch with `npm run dev`.

---

## Interview

Ask the user:

1. Project name? (e.g. MyApp, ShopFlow, DashboardUI)
2. Project location? (workspace root or custom path)
3. Styling approach?
   a) Tailwind CSS (recommended)
   b) CSS Modules
   c) Styled Components
4. Which features to include?
   a) React Router (navigation)
   b) Redux Toolkit (state management)
   c) Axios API layer
   d) ESLint + Prettier

## Generation Steps

1. Scaffold with `npm create vite@latest <name> -- --template react-ts`
2. Install selected dependencies
3. Create Atomic Design folder structure under `src/components/`
4. Generate pages: Home, Login, Register
5. Wire React Router with protected route guard
6. Create example components for each atomic level
7. Add README with run instructions

---

*This is a skill used by the @REACT_JS_AGENT agent.*
