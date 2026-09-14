---
name: architecture.skill
description: Define and enforce a React JS component architecture using Atomic Design and feature-based folder structure
---

# Skill: React Application Architecture

## Purpose

Define maintainable React architecture combining **Atomic Design** with a **feature-based module** structure, while preserving existing project conventions.

## Principles

- Use strict TypeScript, function components, hooks, React Router, and the project's selected state library.
- Keep presentation, hooks/business logic, transport (API clients), and state separate.
- Organize by feature; keep shared primitives in `components/` and app-wide providers/config in `lib/`.
- Prefer typed props interfaces and explicit component contracts.
- Keep route/page components thin: compose feature components and delegate behavior to hooks.
- Keep dependencies directed inward: components call hooks, hooks call API clients/state, API clients own transport.

## Recommended Structure

```text
src/
├── assets/                  # Static assets (images, fonts, icons)
├── components/              # Atomic Design shared component library
│   ├── atoms/                # Smallest building blocks (Button, Input, Label, Icon)
│   ├── molecules/            # Groups of atoms (FormField, SearchBar, Card)
│   ├── organisms/             # Complex UI sections (Header, Sidebar, DataTable)
│   └── templates/             # Page layout wrappers (AuthLayout, DashboardLayout)
├── features/                # Feature-based modules (self-contained)
│   ├── auth/
│   │   ├── components/       # Feature-specific components
│   │   ├── hooks/             # Feature-specific hooks
│   │   ├── api/                # API calls for this feature
│   │   ├── store/              # State slice/queries for this feature
│   │   └── index.ts            # Public API of the feature
│   └── dashboard/
│       ├── components/
│       ├── hooks/
│       ├── api/
│       ├── store/
│       └── index.ts
├── hooks/                    # Global reusable custom hooks
├── lib/                       # Third-party library wrappers and config
├── pages/                     # Route-level page components
├── routes/                    # React Router configuration
├── store/                     # Root state configuration (when a global store is selected)
├── styles/                    # Global styles, design tokens, theme
├── types/                     # Shared TypeScript types and interfaces
└── utils/                     # Pure utility functions
```

Feature-specific code belongs under `features/<feature>` rather than a global utility folder. Keep API models separate from domain view models and map between them.

## Atomic Design Levels

| Level | Purpose | Examples |
|---|---|---|
| **Atoms** | Single-purpose, no dependencies | Button, Input, Label, Badge, Spinner |
| **Molecules** | Composed of atoms | FormField, SearchBar, AlertMessage |
| **Organisms** | Complex UI blocks | Header, Footer, DataTable, Sidebar |
| **Templates** | Layout wrappers | AuthLayout, DashboardLayout |
| **Pages** | Route-level, wired to features | LoginPage, HomePage, ProfilePage |

## Feature Module Rules

- Each feature is **self-contained**: components, hooks, API calls, and state live inside the feature folder.
- Features expose a single `index.ts` public API — no deep imports from outside.
- Cross-feature communication happens through the global store or React Context, never via direct imports.

## Component Rules

- One component per file, named exports only (pages are the exception).
- File name matches component name (PascalCase).
- Props interface defined in the same file or a co-located `types.ts`.
- No business logic inside presentational components; hooks extract all stateful and side-effect logic.
- A component may only import from its own tier or smaller tiers (organism -> molecules/atoms; molecule -> atoms). Never import an organism into a molecule or atom.

## Data Flow

```text
Router -> page component -> hook (feature/API/state) -> typed API client -> HTTP API
       <- view model <- state/query cache <- mapped response
```

Do not place database access, secrets, or server authorization in React code. Route guards improve navigation but do not replace server authorization.

## Accessibility Standards

Follow W3C WCAG 2.0, 2.1, and 2.2 by default, targeting **WCAG 2.2 Level AA** as the primary conformance target. Map requirements and review findings to the relevant WCAG version and success-criterion ID; never claim conformance without automated and manual evidence.

- Use semantic HTML landmarks (`<header>`, `<nav>`, `<main>`, `<footer>`) over generic `<div>`s.
- Every page/view has exactly one clear, correctly nested heading hierarchy (`h1`–`h6`).
- All form controls have associated `<label>`s or `aria-label`/`aria-labelledby`.
- All interactive elements are keyboard-reachable and operable (`Tab`, `Enter`, `Space`, `Escape`, arrow keys where applicable).
- Visible focus indicators must never be suppressed (`outline: none` without a replacement is forbidden).
- Text and interactive elements meet WCAG contrast ratios (4.5:1 normal text, 3:1 large text/UI components).
- Icon-only buttons/controls require an accessible name via `aria-label` or visually hidden text.
- Dialogs, modals, and drawers trap focus while open, restore focus to the trigger on close, and are dismissible via `Escape`.
- Respect `prefers-reduced-motion` for animations and transitions.
- Use `aria-live` regions (or equivalent) to announce async status, validation, and error messages.
- Design mobile-first; prevent content overflow and unexpected layout shifts across breakpoints.
- Explicitly implement loading, empty, error, pending, validation, disabled, unauthorized, and success states for every component that has them.

## Validation

Test route boundaries, hook behavior, state transitions, mapping, errors, and accessibility with React Testing Library-based unit/component tests, plus production builds. Preserve the selected state library and existing project style.

## Capabilities

- Generate the full folder structure for a new project.
- Audit an existing project and report structural violations.
- Suggest a refactoring plan to migrate to this architecture.
- Generate example components at each atomic level.
