# Next.js UI Task

Implement the requested UI change using `agents/nextjs-ui.agent.md`.

Apply these skills:

- `skills/architecture.skill.md`
- `skills/ui_standards.skill.md`
- `skills/shadcn.skill.md` when the project already uses shadcn/ui or the user
	explicitly requests it

## Implementation Requirements

- Inspect the existing routes, components, Tailwind/theme tokens, icon library,
	and design conventions before editing.
- Preserve App Router boundaries: use Server Components by default and add
	`"use client"` only for browser APIs, event handlers, local interaction, or
	client-only libraries.
- Keep server data fetching, authentication, database access, and private
	environment variables out of Client Components.
- Keep feature-specific components near their feature and reusable primitives in
	the existing shared component location.
- Use semantic landmarks, accessible names, visible focus states, keyboard
	navigation, and correct heading hierarchy.
- Handle loading, empty, error, pending, disabled, and success states where the
	changed workflow requires them.
- Use URL search parameters for shareable search, filters, sorting, and
	pagination; validate them before use.
- Preserve responsive behavior and prevent text overflow or layout shifts.
- Do not add a UI library or dependency unless requested or already present.

## Validation and Report

Run the project's applicable lint, typecheck, component/unit, and browser tests.
Manually verify the changed route at supported desktop and mobile viewports when
browser tooling is available. Report changed routes/components, the Server or
Client Component decisions, commands run with pass/fail results, and residual
risk.