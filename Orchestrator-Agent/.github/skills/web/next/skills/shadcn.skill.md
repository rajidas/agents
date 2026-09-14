# Skill: shadcn/ui Generator

## Purpose

Generate and compose shadcn/ui components for Next.js applications only when the
project already uses shadcn/ui or the user explicitly selects it. Treat the
components as project-owned source code and follow the repository's existing
Tailwind, Radix, and component conventions.

## Discovery Before Generation

Before adding a component, inspect:

- `components.json` and the configured aliases.
- The existing `components/ui` directory and barrel exports.
- Tailwind configuration, theme tokens, and utility helpers such as `cn`.
- Existing components with similar semantics or behavior.
- The installed versions of `@radix-ui/*`, Tailwind, and related packages.

Do not install or regenerate a primitive that already exists. Do not introduce
shadcn/ui into a project that uses another established design system unless the
request explicitly asks for migration or coexistence.

## Component Boundaries

- Keep generic primitives in `components/ui`, treated as the Atomic Design
  "atoms" tier from `skills/architecture.skill.md` (composed primitives such as
  `Combobox` or `DataTable` count as molecules).
- Keep feature-specific compositions near the owning feature.
- Keep pages and layouts responsible for composition, not primitive internals.
- Use Server Components by default. Add `"use client"` only for state,
	event handlers, browser APIs, or Radix interactions that require it.
- Do not import server-only services, database clients, or private environment
	variables into interactive components.
- Pass serializable props across the client boundary and keep data fetching on
	the server when possible.

## Accessibility and Interaction

- Preserve Radix keyboard navigation, focus management, and ARIA behavior.
- Use the correct primitive for the semantic job: `Dialog` for modal dialogs,
	`AlertDialog` for destructive confirmation, `DropdownMenu` for actions, and
	`Select` or `Combobox` for choosing values.
- Give every icon-only button an accessible label and a tooltip when the icon is
	unfamiliar.
- Connect labels, descriptions, validation messages, and controls correctly.
- Ensure dialogs can close with Escape, focus is restored, and destructive
	actions require an intentional confirmation when appropriate.
- Include disabled, pending, empty, and error states without hiding useful
	feedback from assistive technology.

## Styling and Composition

- Reuse theme tokens and `cn` rather than scattering arbitrary colors or
	one-off spacing values.
- Preserve the project's responsive breakpoints and density.
- Keep variants explicit with the existing class-variance pattern when present.
- Avoid deeply nested cards, excessive rounded containers, and decorative UI
	that competes with the primary workflow.
- Use icons from the project's installed icon library instead of hand-drawn SVG
	icons when an equivalent exists.
- Keep component dimensions stable so loading labels and validation messages do
	not cause layout shifts.

## Generation Steps

1. Identify the user interaction and its semantic control.
2. Confirm an existing primitive does not already solve the problem.
3. Add the smallest required shadcn/ui primitive or composition.
4. Wire typed props, validation, pending state, and error handling.
5. Verify the Server Component and Client Component boundary.
6. Test keyboard navigation, focus behavior, responsive layout, and the main
	 success and failure states.

## Completion Checklist

- Existing project conventions and primitives are reused.
- Radix accessibility behavior is preserved.
- Interactive code has an intentional client boundary.
- Icon-only controls have accessible names.
- Loading, disabled, validation, and error states are visible and usable.
- No secrets or server-only imports enter the client bundle.
- The component works at the project's supported viewport sizes.