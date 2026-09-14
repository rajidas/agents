---
description: "Create a new screenshot-driven website or frontend application with responsive, accessible, tested UI"
name: "Screenshot UI Application"
argument-hint: "Attach desktop/tablet/mobile screenshots and describe the application to build"
agent: "agent"
---

# Screenshot-Driven Frontend Application

Build a new website, web application, dashboard, portal, landing page, admin
panel, catalog, form, detail page, workflow, or other frontend interface from
the screenshot images attached to the current user prompt. This is a new
project unless the user explicitly requests changes to an existing project.

## Framework and Project Discovery

Before generating code, reconcile the complete prompt and collect only values
that are genuinely missing. Ask only the first missing question, one question
per turn. Do not generate code, folders, architecture, routes, components, or
UI until required new-project values are known.

Collect or infer:

- `PROJECT_ID`, `PROJECT_NAME`, and `PROJECT_PATH`;
- `APPLICATION_TYPE` and `CATEGORY` from clear domain words;
- `FRAMEWORK` and `TECH_STACK`;
- `DATA_TYPE`, `UI_STYLE`, `AUTH_TYPE`, and `STATE_MANAGEMENT`;
- `ACCESSIBILITY_LEVEL` and `TESTING_LEVEL`.

Detect the framework from the prompt or existing requirements. Support Angular,
React, Next.js, Vue, Nuxt, Svelte, SvelteKit, Solid, Astro, and other requested
frontend frameworks. If no framework is specified, ask which framework to use;
do not silently choose one. Follow the selected framework's current official
scaffolding, routing, component, state, styling, and testing conventions.

Validate that `PROJECT_PATH` is outside the `Orchestrator-Agent` repository and
is empty or newly created. Never scaffold inside this repository and never
overwrite an existing application. Use the framework's standard generator
only after destination validation.

## Non-Negotiable Design Gate

1. Inspect every attached screenshot before writing or changing UI code. If an
   expected image is unavailable, stop and report `BLOCKED: screenshot
   unavailable`; do not invent a replacement design.
2. Record screenshot analysis before implementation: target viewport
   dimensions, page or screen regions, hierarchy, exact visible text,
   typography, colors, spacing, borders, radii, shadows, icons, images,
   controls, states, and interactions.
3. Treat supplied screenshots as the visual source of truth. Reuse supplied
   or existing assets when available. Do not substitute generic imagery,
   invented sections, placeholder cards, or unsupported content.
4. Inspect desktop, tablet, and mobile screenshots separately when supplied.
   Implement each viewport from its own evidence; do not infer mobile layout
   from desktop when a mobile screenshot is attached.
5. After implementation, compare the rendered website or application with each
   supplied screenshot at its target viewport. Record mismatches, corrections,
   and visual evidence before completion.

## Implementation Rules

- Inspect the selected framework version, package manager, scripts, routes,
  shell, design tokens, assets, dependencies, and test configuration before
  implementation.
- Use the selected framework's idiomatic architecture, strict type checking
  where available, reusable components, and clear UI/data boundaries.
- Preserve the selected or existing styling and component library. Do not add
  competing libraries or dependencies without a documented need.
- Implement the requested screenshot surface; do not assume it is a home page.
- Implement visible or clearly implied interactions with real behavior.
- Provide loading, empty, error, pending, success, disabled, and unauthorized
  states where relevant.
- Keep API calls and business rules out of presentational components.
- Do not invent features, routes, content, or assets that are unsupported by
  the prompt, screenshots, or application requirements.

## Accessibility

Target WCAG 2.2 Level AA while preserving applicable WCAG 2.0 and 2.1
criteria. Include logical headings, semantic landmarks, accessible names for
controls and images, keyboard navigation, visible focus, sufficient contrast,
usable touch targets, reduced-motion support, responsive text reflow, and
accessible error/status messaging where applicable. Validate with automated
checks and keyboard/manual checks. Report Pass, Partial, or Blocked with
criterion IDs and residual risks.

## Testing and Completion

1. Inspect project scripts, runner configuration, lockfile, dependencies, and
   existing specs before selecting commands or adding tools.
2. Reuse the existing compatible test runner and browser configuration. If a
   required spec file is missing, create the smallest focused spec using the
   project's established conventions before running the suite. Do not weaken
   assertions to make tests pass.
3. Add or update tests for the requested screen, navigation, visible controls,
   relevant UI states, keyboard access, accessibility, and responsive behavior.
4. Run focused tests first, then the complete configured test suite, lint,
   typecheck, production build, accessibility checks, and configured browser
   tests. Validate desktop, tablet, and mobile viewports and supported browsers.
5. Do not claim completion until required checks pass. Report every command,
   result, created spec, screenshot comparison evidence, accessibility result,
   and remaining blocker.

## Required Output

Return:

- detected framework and application type;
- screenshot analysis and target viewport matrix;
- architecture, routes, and affected files;
- implementation summary;
- responsive and accessibility decisions;
- test/spec changes;
- exact validation commands and results;
- screenshot comparison evidence and residual risks.