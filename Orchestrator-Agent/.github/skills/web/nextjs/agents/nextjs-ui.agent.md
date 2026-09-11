## Default UI Scaffold Requirements

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