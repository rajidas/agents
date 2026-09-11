## Default UI Scaffold Requirements

## Flowchart Responsibilities

This agent owns the UI portions of two input-specific stages:

- **Design Analysis Agent**: for Figma input, inspect the supplied node through
	the configured MCP server; for an attached screenshot or image, inspect the
	available visual evidence directly. Record hierarchy, layout, typography,
	spacing, colors, assets, interactions, responsive rules, and assumptions.
- **UI Layout Generation Agent**: after source-specific analysis is recorded,
	generate the App Router page/layout composition and Atomic Design components.
	Map every analyzed region to an implementation surface and include required
	loading, empty, error, pending, success, validation, and responsive states.

Do not generate UI before the relevant analysis output exists. Do not claim
Figma analysis without MCP inspection; do not invent missing image details.

## Figma-to-Next.js Workflow
### Fidelity gate

Figma-led work is evidence-driven. Before implementation, create a design inventory recording the Figma file/node URL, target frame dimensions, section hierarchy, component and variant mapping, typography/color/spacing tokens, asset manifest, interaction/state matrix, responsive rules, and implementation file for each mapped region. Every visible region must be traceable.

Do not use emoji, generic placeholders, random remote images, CSS-drawn substitutes, or invented icons when Figma exposes the exact asset, icon, illustration, font, or component. Retrieve it through MCP or use a verified project equivalent. If that is impossible, stop and record the blocker instead of silently approximating it. Do not claim Figma analysis without successful MCP inspection or visual parity without a rendered comparison.



When the request includes a Figma file, frame, component, or node:

### 1. Establish the design target

- Require a Figma URL or node ID before implementation. Accept a direct node
	URL such as `https://www.figma.com/design/<file-key>/<file-name>?node-id=1-2`.
- Parse the file key and node ID from the URL when possible. If the URL has no
	node ID, inspect the file entry point only when the user explicitly requests
	the whole file.
- Confirm the target route, viewport(s), and required interactions. Do not
	invent missing product behavior; record assumptions in the implementation
	summary.
- Verify that the workspace contains the target Next.js application. If it does
	not, ask whether to scaffold a new application in a separate, user-approved
	directory before generating UI code; never scaffold into this agent kit.
- Use the configured `figma` MCP server for all Figma inspection. Never ask the
	user to paste secrets into chat and never expose access tokens to client code.

### 2. Inspect before coding

Use Figma MCP in this order, selecting the narrowest available node scope:

1. Read the target node and its children to recover the page hierarchy,
	 auto-layout direction, sizing constraints, padding, gaps, alignment, and
	 responsive variants.
2. Inspect component instances, component sets, variant properties, and
	 variable references. Follow instances to their main components when the
	 structure or states are ambiguous.
3. Extract typography (family, weight, size, line height, letter spacing),
	 colors, opacity, borders, radii, shadows, spacing, grid definitions, and
	 interaction states into a design model before writing JSX.
4. Identify icons and image fills. Prefer the project's icon library for icons;
	 download or retrieve Figma assets through MCP into the project's existing
	 public/assets convention. Do not replace an inspectable asset with a generic
	 placeholder.
5. Request a rendered screenshot of the target node when available. Use it as
	 a visual reference, not as a substitute for inspecting structure and tokens.

Maintain this design model in the task notes or implementation summary:

```text
pages, sections, components, tokens, assets, interactions, responsiveRules
```

If the Figma MCP server is unavailable, stop before implementation and report
the missing server/configuration plus the exact target URL or node ID required.
Do not claim that a design was analyzed from a screenshot or description alone.

### 3. Map Figma to the existing application

Before creating a component, search the project for matching routes, layouts,
atoms, molecules, organisms, templates, hooks, tokens, utilities, and assets.
Map Figma components to existing code by responsibility and behavior, not by
name alone:

- Reuse an existing component when its semantics and states match.
- Extend it with typed variants when the Figma design adds a legitimate state.
- Create a new component only for behavior or structure that does not exist.
- Keep shared visual primitives in the existing atomic tier and keep
	feature-specific components near their owning feature.
- Do not duplicate a component merely to match a different screen.

Keep page data and composition in the App Router route or Server Component.
Keep interactive state in the smallest necessary Client Component and pass only
serializable props across the boundary. Use existing design tokens first; add
named tokens for repeated Figma values rather than scattering raw values.

### 4. Implement with fidelity

- Preserve the project's App Router, TypeScript, Tailwind, naming, and import
	conventions. Follow `skills/architecture.skill.md` and existing local rules.
- Match hierarchy, dimensions, alignment, type scale, colors, spacing, states,
	assets, and responsive constraints. Implement desktop, tablet, and mobile
	behavior inferred from Figma constraints or explicitly stated assumptions.
- Implement visible hover, focus, pressed, disabled, loading, empty, and error
	states when they exist in Figma or are required by the workflow.
- Use semantic HTML, keyboard support, visible focus indicators, labels, and
	appropriate landmarks. Never use visual styling as a substitute for an
	accessible name.

### 5. Validate against Figma

After implementation:

1. Run the narrowest relevant lint, typecheck, unit test, and build checks.
2. Start the app and use Playwright or the available browser tooling at every
	 supplied Figma viewport, plus a narrow mobile viewport.
3. Capture implementation screenshots and compare them with the Figma render.
	 Check layout bounds, alignment, typography, colors, spacing, assets, overflow,
	 responsive transitions, and interactive states.
4. Fix the largest visual or behavioral mismatch first, then repeat the focused
	 check. Do not replace working application behavior just to improve pixels.
5. Report the Figma URL/node, design model summary, reused/new components,
	 affected files, viewport checks, commands run, and any unresolved mismatch.

Do not declare visual parity without a rendered comparison when browser tooling
is available. If screenshots or Figma renders cannot be obtained, state that
the validation is structural only and identify the residual risk.

When a new Next.js application is created:

Do not keep the default Next.js starter page.

Immediately replace starter content with a production-ready foundation.

The foundation must be a ready-to-use example skeleton. Dummy copy is allowed,
but every link, layout region, and component must be valid and easy to replace.

Generate:

### Global Layout

- Responsive Header
- Responsive Footer
- Main Navigation
- Content Container
- Skip-to-content link

### Home Page

- Dummy hero section with a clear page heading and primary action
- Dummy feature section with three or more feature cards
- Search bar with a visible label and example placeholder
- Dummy CTA section with a usable link or button
- Footer with grouped navigation and example contact/legal links

### Shared Components

Follow the Atomic Design hierarchy from `skills/architecture.skill.md`:

components/
├── atoms/
│   ├── button.tsx
│   └── input.tsx
├── molecules/
│   ├── search-box.tsx
│   ├── feature-card.tsx
│   └── nav-item.tsx
└── organisms/
    ├── header.tsx
    ├── footer.tsx
    └── navigation.tsx

The root layout must import the shared header and footer organisms, and the
page must use the shared navigation and feature-card components rather than
duplicating their markup. Never place an organism inside `atoms/` or
`molecules/`, and never let an atom or molecule import an organism.

### User Experience

- Search functionality
- Loading States
- Error States
- Empty States
- Mobile Navigation
- Active and hover states for links and actions
- Clear focus indicators and keyboard navigation

### Content Experience

When the landing-page mode includes content:

- Render category navigation and category filters using validated, shareable URL
	search parameters. Selecting a category must change the visible post results.
- Render published blog posts with a functional search field, category filter,
	sorting control, and pagination-ready state. Search and filter values must be
	preserved in the URL and applied to the displayed results.
- Use a typed, replaceable data source for the example content. Do not present
	static controls that have no effect, and do not add a backend just to demo the
	landing page when a local server-side collection is sufficient.
- Add a blog post detail route using a stable slug and show published content
	only on public routes.
- Include visible loading, empty-result, invalid-query, and recoverable error
	states for the content experience.

When the admin-dashboard mode is selected:

- Add protected category and blog post management screens.
- Provide list, create, view, edit, and delete workflows with clear pending,
	validation, error, success, and delete-confirmation states.
- Keep admin navigation and controls separate from the public landing experience.
- Do not rely on hidden buttons for security; the server must authorize every
	mutation.

### Styling

- Tailwind CSS
- Responsive Design
- Modern Layout
- Accessibility Compliance

### Skeleton Acceptance Criteria

- The header appears on every route through `app/layout.tsx`.
- The footer appears after the main content and does not overlap it.
- Header navigation works on desktop and has a usable mobile presentation.
- Dummy links use valid destinations or clearly intentional placeholder targets.
- Search changes the displayed blog posts and preserves its query in the URL.
- Category filtering changes the displayed blog posts and can be shared by URL.
- Search and filters have accessible labels, keyboard support, and a clear/reset
	action.
- The page contains no default create-next-app branding or starter instructions.
- The skeleton passes lint, typecheck, and production build checks.

The default Next.js starter page must never remain in the final solution.