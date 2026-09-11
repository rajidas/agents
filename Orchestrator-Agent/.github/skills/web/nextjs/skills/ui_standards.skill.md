# Skill: Next.js UI Standards

## Purpose

Generate consistent, accessible, responsive UI for Next.js App Router
applications. Apply these standards to layouts, pages, feature components, and
shared navigation while preserving any existing design system.

## Required Application Shell

When the product needs these elements, compose them as a predictable shell:

```text
app/layout.tsx
	-> Header
	-> Sidebar or mobile navigation
	-> Breadcrumbs
	-> Main content
	-> Footer
```

For a new example or starter application, generate the full shell with a
responsive header, navigation, main content container, and footer. Use concise
dummy content that demonstrates the intended structure and is easy to replace.
For an existing product, generate only the regions relevant to the requested
workflow and preserve its current navigation.

## Component Responsibilities

- `Header`: example brand, category/section navigation derived from the
	project's category (not a single generic link), account actions when
	relevant, and mobile menu trigger or usable mobile navigation.
- `Sidebar`: section navigation, active state, collapse behavior, and keyboard
	access for dashboard-style applications.
- `Breadcrumbs`: reflect the current route hierarchy and use links except for
	the current page.
- `Search Bar`: expose a real label, preserve query state in the URL when search
	is shareable, and provide a clear pending or empty state. Pair it with at
	least one filter control and one sort control so results can be narrowed and
	reordered, not just searched.
- `Responsive Grid`: use stable columns, consistent gaps, and a meaningful
	single-column mobile layout.
- `Footer`: its own footer-category navigation (e.g., Company, Support, Legal,
	Resources) plus contact/legal links — never copyright text alone.

## Ready-to-Use Skeleton

For a new Next.js app, follow `skills/architecture.skill.md`'s Atomic Design
hierarchy for these shared components unless the user requests a different
structure:

```text
components/
	atoms/
		button.tsx
		input.tsx
		label.tsx
		icon.tsx
	molecules/
		search-box.tsx
		nav-item.tsx
		feature-card.tsx
	organisms/
		header.tsx
		footer.tsx
		navigation.tsx
	templates/
		home-template.tsx
```

Wire `Header` and `Footer` through `app/layout.tsx`. The home page should show a
clear heading, short dummy description, primary action, three feature cards, a
search example with filter and sort controls, and a CTA. Include a skip link to
`main`, valid example routes, and a footer that follows the content without
overlap. Keep these components as Server Components unless interaction
genuinely requires a Client Component.

A bare header/footer with no category navigation, no search/filter/sort, and no
aligned content sections is never an acceptable result, even for a "simple" or
unthemed request. When no specific project category is selected, still invent a
plausible set of header/footer categories and at least two unique sections
(e.g., featured items rail, stats band, FAQ) so the app is not a generic
starter shell.

## App Router and Data Boundaries

- Keep layouts and static page composition as Server Components by default.
- Use Client Components only for menus, filters, dialogs, form state, and other
	browser interactions.
- Fetch initial page data on the server and pass minimal serializable view models
	to interactive children.
- Keep database access, authentication, and private environment variables out of
	UI components.
- Use URL search parameters for shareable filters, sorting, pagination, and
	search state. Validate them before using them in queries.
- Implement `loading.tsx`, `error.tsx`, and meaningful empty states for routes
	that fetch or mutate data.

## Accessibility Standards

- Use semantic landmarks: `header`, `nav`, `main`, `aside`, and `footer`.
- Provide one clear page heading and a logical heading hierarchy.
- Associate every form control with a visible label or an equivalent accessible
	name.
- Ensure keyboard users can reach navigation, dialogs, menus, filters, and forms.
- Keep visible focus indicators and maintain sufficient color contrast.
- Give icon-only buttons an accessible label and a tooltip when needed.
- Announce validation, pending, and error feedback without relying on color alone.
- Respect reduced-motion preferences for non-essential animation.

## Visual and Responsive Standards

- Use Tailwind and the project's configured tokens, spacing, typography, and
	breakpoints.
- Use TypeScript props and shared components instead of duplicated markup.
- Design for mobile first, then add layouts for larger viewports.
- Keep text inside its parent, prevent layout shifts, and define stable sizes for
	controls, grids, tables, and loading placeholders.
- Use cards only for repeated items, dialogs, or genuinely framed tools. Keep
	page sections as full-width bands or unframed layouts.
- Use familiar icons in tool buttons and text labels for important actions.
- Keep visual hierarchy purposeful: compact controls for dashboards and readable
	content widths for detail pages.

## State and Interaction Requirements

For data-driven screens, account for:

- Loading and pending states.
- Empty collections and first-use guidance.
- Validation errors near the relevant control.
- Recoverable server errors with a retry path.
- Disabled states during mutations.
- Success feedback and navigation after completion.
- Permission-aware actions that are enforced on the server as well as hidden or
	disabled in the UI.

## Generation Steps

1. Identify the page type, primary task, content hierarchy, and responsive needs.
2. Inspect existing components, tokens, Tailwind setup, and icon library.
3. Compose the shell and shared primitives before feature-specific markup.
4. Choose Server or Client Components deliberately for each interactive region.
5. Add all relevant loading, empty, error, pending, and success states.
6. Verify keyboard navigation, responsive layout, contrast, and text fit.
7. Add focused browser or component tests for the primary workflow.

## Completion Checklist

- The page has a clear heading, landmark structure, and primary action.
- Header, sidebar, search, breadcrumbs, grid, and footer are used only when
	relevant to the product workflow.
- UI is responsive without overlap or layout shifts.
- Interactive controls are keyboard accessible and have useful names.
- Server-only code stays outside Client Components.
- Loading, empty, error, pending, and success states are handled where relevant.
- Tailwind, TypeScript, and existing design-system conventions are followed.