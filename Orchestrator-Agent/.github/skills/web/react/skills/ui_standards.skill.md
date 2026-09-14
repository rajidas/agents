---
name: ui_standards.skill
description: React UI standards for shell composition, component/design-system usage, responsive design, and accessibility.
---

# Skill: React UI Standards

## Required Shell

For a new application compose:

```text
App shell -> Header -> navigation/mobile menu -> breadcrumbs -> main -> Footer
```

Use a category or section navigation derived from the selected application, a usable mobile menu, clear page heading, footer groups, and semantic landmarks. Include real search/filter/sort behavior when the screen lists data.

## Component Rules

- Use function components with typed props and keep reusable primitives in `components/{atoms,molecules,organisms,templates}`.
- Keep feature-specific components under their feature (`features/<feature>/components`).
- Use Tailwind CSS, Material UI, Chakra UI, or shadcn/ui according to the configured stack; do not introduce competing libraries.
- Keep data access in hooks/API clients and pass typed view models to presentational components.
- Use typed forms (React Hook Form or the existing convention) with visible labels, validation, pending, disabled, and success feedback.

## Ready-to-Use Skeleton

For a new React app, follow `skills/architecture.skill.md`'s Atomic Design hierarchy for shared components unless the user requests a different structure:

```text
components/
	atoms/
		Button.tsx
		Input.tsx
	molecules/
		SearchBox.tsx
		FeatureCard.tsx
		NavItem.tsx
	organisms/
		Header.tsx
		Footer.tsx
		Navigation.tsx
	templates/
		HomeTemplate.tsx
```

Wire `Header` and `Footer` through the root layout/route. The home page should show a clear heading, short dummy description, primary action, three feature cards, a search example with filter and sort controls, and a CTA. Include a skip link to `main`, valid example routes, and a footer that follows the content without overlap.

## Accessibility and Responsive Design

Follow W3C WCAG 2.0, 2.1, and 2.2 by default, targeting WCAG 2.2 Level AA.
Map accessibility requirements and tests to version and success-criterion IDs;
do not claim conformance without automated and manual evidence.

Use semantic HTML, one clear page heading, associated labels, keyboard navigation, visible focus, sufficient contrast, accessible icon labels, focus management for dialogs/menus, and reduced-motion support. Design mobile first, prevent text overflow and layout shifts, and define stable dimensions for controls, grids, tables, and loading placeholders.

## State Requirements

Handle loading, empty, validation, recoverable server errors, retry, disabled mutations, success feedback, unauthorized navigation, and not-found routes where relevant. Enforce permissions on the server as well as in the UI; a hidden or disabled client control is never sufficient authorization.

## Completion Checklist

- Routes and shell are linked and usable on desktop and mobile.
- Components use typed props and intentional state boundaries.
- Controls are keyboard accessible and have useful names.
- Loading, empty, error, pending, and success states are visible where relevant.
- No secrets or server-only assumptions enter client code.
- Focused React Testing Library component tests (or browser tests) cover the primary workflow, asserting accessible roles/labels rather than implementation details.
