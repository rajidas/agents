---
name: react-architecture_skill
description: Define and enforce a React JS component architecture using Atomic Design and feature-based folder structure
---

# React Application Architecture Skill

This skill defines the component architecture for a React JS project.

## Architecture Overview

This skill applies **Atomic Design** combined with a **feature-based module** structure to organize large React applications.

---

## Folder Structure

```
src/
├── assets/                  # Static assets (images, fonts, icons)
├── components/              # Atomic Design shared component library
│   ├── atoms/               # Smallest building blocks (Button, Input, Label, Icon)
│   ├── molecules/           # Groups of atoms (FormField, SearchBar, Card)
│   ├── organisms/           # Complex UI sections (Header, Sidebar, DataTable)
│   └── templates/           # Page layout wrappers (AuthLayout, DashboardLayout)
├── features/                # Feature-based modules (self-contained)
│   ├── auth/
│   │   ├── components/      # Feature-specific components
│   │   ├── hooks/           # Feature-specific hooks
│   │   ├── api/             # API calls for this feature
│   │   ├── store/           # Redux slice for this feature
│   │   └── index.ts         # Public API of the feature
│   └── dashboard/
│       ├── components/
│       ├── hooks/
│       ├── api/
│       ├── store/
│       └── index.ts
├── hooks/                   # Global reusable custom hooks
├── lib/                     # Third-party library wrappers and config
├── pages/                   # Route-level page components
├── routes/                  # React Router configuration
├── store/                   # Redux store root
├── styles/                  # Global styles, design tokens, theme
├── types/                   # Shared TypeScript types and interfaces
└── utils/                   # Pure utility functions
```

---

## Atomic Design Levels

| Level | Purpose | Examples |
|---|---|---|
| **Atoms** | Single-purpose, no dependencies | Button, Input, Label, Badge, Spinner |
| **Molecules** | Composed of atoms | FormField, SearchBar, AlertMessage |
| **Organisms** | Complex UI blocks | Header, Footer, DataTable, Sidebar |
| **Templates** | Layout wrappers | AuthLayout, DashboardLayout |
| **Pages** | Route-level, wired to features | LoginPage, HomePage, ProfilePage |

---

## Feature Module Rules

- Each feature is **self-contained**: components, hooks, API calls, and state live inside the feature folder.
- Features expose a single `index.ts` public API — no deep imports from outside.
- Cross-feature communication happens through the Redux store or React Context, never via direct imports.

---

## Component Rules

- One component per file.
- File name matches component name (PascalCase).
- Props interface defined in the same file or a co-located `types.ts`.
- No business logic inside presentational components.
- Hooks extract all stateful and side-effect logic.

---

## Capabilities

- Generate the full folder structure for a new project
- Audit an existing project and report structural violations
- Suggest refactoring plan to migrate to this architecture
- Generate example components at each atomic level

---

*This is a skill used by the @REACT_JS_AGENT agent.*
