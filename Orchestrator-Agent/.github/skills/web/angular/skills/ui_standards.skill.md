# Skill: Angular UI Standards

## Required Shell

For a new application compose:

```text
App shell -> Header -> navigation/sidebar -> breadcrumbs -> main -> Footer
```

Use a category or section navigation derived from the selected application, a usable mobile menu, clear page heading, footer groups, and semantic landmarks. Include real search/filter/sort behavior when the screen lists data.

## Component Rules

- Use standalone components and keep reusable primitives in `shared`.
- Keep feature-specific components under their feature.
- Use Angular Material, PrimeNG, or Tailwind according to the configured stack; do not introduce competing libraries.
- Keep data access in services/facades and pass typed view models to presentational components.
- Use typed reactive forms with visible labels, validation, pending, disabled, and success feedback.

## Accessibility and Responsive Design

Follow W3C WCAG 2.0, 2.1, and 2.2 by default, targeting WCAG 2.2 Level AA.
Map accessibility requirements and tests to version and success-criterion IDs;
do not claim conformance without automated and manual evidence.

Use semantic HTML, one clear page heading, associated labels, keyboard navigation, visible focus, sufficient contrast, accessible icon labels, focus management for dialogs/menus, and reduced-motion support. Design mobile first, prevent text overflow and layout shifts, and define stable dimensions for controls, grids, tables, and loading placeholders.

## State Requirements

Handle loading, empty, validation, recoverable server errors, retry, disabled mutations, success feedback, unauthorized navigation, and not-found routes where relevant. Enforce permissions on the server as well as in the UI.

## Completion Checklist

- Routes and shell are linked and usable on desktop and mobile.
- Components use typed inputs/outputs and intentional state boundaries.
- Controls are keyboard accessible and have useful names.
- Loading, empty, error, pending, and success states are visible where relevant.
- No secrets or server-only assumptions enter client code.
- Focused `TestBed` component tests (or browser tests) cover the primary workflow, asserting accessible roles/labels rather than implementation details.
