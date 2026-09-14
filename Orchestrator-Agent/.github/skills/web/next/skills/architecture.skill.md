# Skill: Next.js Application Architecture

## Purpose

Define a maintainable architecture for Next.js App Router applications. Use this
skill when scaffolding a project, adding a feature, or deciding where application
logic belongs. Preserve the existing project structure when it already follows
these boundaries.

## Architectural Principles

- Use the App Router and TypeScript for all new application code.
- Prefer Server Components by default. Add `"use client"` only for browser APIs,
  local interactive state, event handlers, or client-only libraries.
- Keep UI rendering, application use cases, transport handlers, and persistence
  separate.
- Keep domain rules independent from Next.js request and response objects.
- Pass serializable data across the Server Component and Client Component
  boundary. Never pass secrets, database clients, or server-only objects.
- Make dependencies point inward: routes and UI may call application services;
  application services may call repositories; repositories own database access.
- Prefer small feature modules over one large global utilities directory.
- Organize shared UI with Atomic Design: atoms compose into molecules, molecules
  compose into organisms, organisms compose into templates, and `app/` route
  files supply real data to templates to render the final page. Never let a
  larger tier reach back into a smaller tier's internals or skip a tier.

## Recommended Structure

Use `src/` when the project has one. For a project without `src/`, apply the same
structure at the repository root.

```text
src/
  app/                         # Routes, layouts, loading, errors, metadata
    (auth)/                    # Authenticated and unauthenticated route groups
    api/                       # Route handlers; transport layer only
    dashboard/                 # Route segments and page composition
    layout.tsx
    page.tsx                   # Atomic "Page": binds real data to a template
  components/                  # Shared presentational components (Atomic Design)
    atoms/                     # Button, Input, Label, Icon, Text
    molecules/                 # SearchBox, FormField, Card, NavigationItem
    organisms/                 # Header, Sidebar, ProductGrid, UserProfile
    templates/                 # Layout skeletons: DashboardTemplate, AuthTemplate
  features/                    # Business capabilities grouped by feature
    users/
      components/              # Feature-specific organisms/molecules
      server/                  # Server-only actions and queries
      user.service.ts          # Feature use cases
      user.repository.ts       # Persistence adapter, when needed
      user.schema.ts           # Validation and input schemas
      user.types.ts            # Feature types
  lib/                         # Cross-cutting infrastructure and utilities
    auth.ts
    db.ts
    env.ts
    errors.ts
    utils.ts
  services/                    # Shared application services only
  types/                       # Shared types with no feature owner
  hooks/                       # Shared client hooks
  test/                        # Test setup and test utilities
```

Do not create a file in `services`, `lib`, or `types` merely to avoid choosing a
feature owner. Put feature-specific code in `features/<feature>`.

## Component Architecture (Atomic Design)

Apply Atomic Design inside `components/` (and inside `features/<feature>/components/`
for feature-owned organisms) instead of a flat, unorganized component folder.

### Atoms

- Smallest building blocks: `Button`, `Input`, `Label`, `Icon`, `Text`.
- No business logic, no data fetching, no feature-specific naming.
- Highly reusable; changes here ripple through every larger tier.

### Molecules

- Simple combinations of atoms: `SearchBox` (Input + Button), `FormField`
  (Label + Input), `Card`, `NavigationItem`.
- Still generic and reusable; may hold small local UI state (open/closed,
  focus) but no server/data-fetching logic.

### Organisms

- Complex, feature-aware components built from molecules and atoms: `Header`,
  `Sidebar`, `ProductGrid`, `UserProfile`.
- May contain business logic and receive server-fetched data as props; keep
  data fetching in the route (`app/`) or a Server Component wrapper, not
  inside the organism itself, unless the organism is explicitly a Client
  Component that needs its own client-side fetch.

### Templates

- Page-level layout skeletons that arrange organisms/molecules/atoms with real
  structure but placeholder or prop-driven content: `DashboardTemplate`,
  `AuthTemplate`, `ProductTemplate`.
- No direct database or service calls; templates only accept and lay out data
  passed in as props.

### Pages (`app/**/page.tsx`)

- The Next.js route file is the Atomic "Page": it fetches real data via a
  Server Component, application service, or repository, then renders a
  `Template` with that data.
- Keep `page.tsx` thin: fetch/compose data, then delegate rendering to a
  template.

### Rules

- A component may only import from its own tier or smaller tiers (organism ->
  molecules/atoms; molecule -> atoms). Never import an organism into a molecule
  or atom.
- Export each tier through an `index.ts` barrel, but never re-export
  server-only modules through it.
- Prefer promoting a component to `features/<feature>/components/` (as an
  organism) when it is not reusable outside one feature; keep `components/`
  reserved for atoms/molecules/organisms/templates used by more than one
  feature or route.

## Layer Responsibilities

### `app/` Route Layer

- Define routes, layouts, metadata, loading states, and error boundaries.
- Compose feature components and call application services or server actions.
- Keep route files thin; do not place reusable business rules in `page.tsx` or
  `route.ts`.
- Use route handlers for external HTTP consumers and webhooks.
- Use Server Actions for mutations initiated by the application's own UI when
  that matches the existing project convention.

### Feature and Application Layer

- Express business use cases with named functions such as
  `createOrder` or `listProjects`.
- Validate input at the boundary, then pass typed values into the use case.
- Enforce authorization in the use case or a shared server-side authorization
  helper, not only by hiding a button in the UI.
- Return domain results or typed errors instead of `NextResponse` objects.

### Repository and Persistence Layer

- Keep Prisma or other database clients in server-only modules.
- Encapsulate queries in repositories when a feature has meaningful persistence
  logic, joins, transactions, or multiple callers.
- Select only fields required by the use case and response; do not expose raw
  database records by default.
- Keep migrations, indexes, uniqueness rules, and transaction boundaries close
  to the data model and documented feature behavior.

### Components and Client State

- Follow the Atomic Design hierarchy above: build organisms from molecules and
  atoms rather than duplicating markup or writing one large monolithic
  component.
- Keep presentational components (atoms, molecules) pure and reusable where
  practical.
- Keep feature-owned organisms near their owning feature when they are not
  shared; keep shared atoms/molecules/organisms/templates in `components/`.
- Use Client Components for interaction, not as a default wrapper around an
  entire page or template.
- Fetch initial data on the server when possible, then pass a minimal serializable
  view model from `page.tsx` into the template and down into organisms.
- Keep loading, empty, error, and pending states explicit in the UI.

## Request and Data Flow

Use this flow for a typical read:

```text
Browser request
  -> app/page.tsx or layout.tsx
  -> feature server query or application service
  -> repository
  -> database
  -> mapped view model
  -> Server Component render
```

Use this flow for a typical mutation:

```text
Form or client event
  -> Server Action or app/api route.ts
  -> authentication and input validation
  -> application service / use case
  -> repository and transaction
  -> cache invalidation or redirect
  -> typed result or HTTP response
```

Do not call a route handler from a Server Component for internal data fetching
unless an external HTTP boundary is explicitly required. Call the server-side
service directly to avoid an unnecessary network hop.

## Server and Client Boundaries

- Keep authentication, authorization, database access, private environment
  variables, filesystem access, and secret-bearing SDKs on the server.
- Add `import "server-only"` to modules that must never enter a client bundle
  when the project supports it.
- Only variables prefixed with `NEXT_PUBLIC_` may be used by browser code, and
  they must not contain secrets.
- Do not import server modules into a Client Component, even indirectly through
  a barrel export.
- Keep client-side state limited to interaction state, optimistic state, and
  data that genuinely must live in the browser.

## Validation, Errors, and Security

- Validate untrusted input with the project's established schema library at API
  and Server Action boundaries.
- Authenticate before reading protected data and authorize every protected
  mutation against the current user or tenant.
- Return consistent status codes and error shapes from route handlers.
- Do not reveal stack traces, SQL details, tokens, passwords, or private model
  fields in browser responses.
- Use environment validation during startup or server execution so missing
  configuration fails clearly.
- Handle webhook signatures, replay protection, rate limits, and idempotency
  where the integration requires them.

## Caching and Revalidation

Choose a cache strategy deliberately for each read:

- Static or infrequently changing content: use the default server cache behavior
  and explicit revalidation when appropriate.
- User-specific or permission-sensitive content: avoid shared caching unless the
  cache key and invalidation policy are proven safe.
- Mutations: invalidate affected tags or paths only after persistence succeeds.
- Do not use cache invalidation as a substitute for authorization or database
  consistency.

Document non-obvious `revalidate`, `dynamic`, `cache`, tag, and redirect choices
next to the owning route or service.

## Testing Expectations

Inspect the project's package manager, `package.json`, scripts, configuration,
and installed dependencies before selecting a test runner. Reuse the existing
compatible runner; when no unit or integration runner exists, use Jest with the
appropriate Next.js, TypeScript, and Testing Library integration. Use Playwright
for browser and critical user-journey coverage. Avoid multiple unit runners
unless a compatibility decision is documented.

For each feature, cover the highest-risk layer with focused tests:

- Unit tests for validation, authorization, and business rules.
- Repository or integration tests for important queries and transactions.
- Route handler or Server Action tests for authentication, invalid input, and
  success responses.
- Playwright coverage for critical user journeys and visible loading, empty, and
  error states.
- Accessibility checks for keyboard navigation, labels, landmarks, and focus
  behavior on interactive flows.

Map every acceptance criterion to a test case and record the test command,
result, coverage evidence, and remaining gaps. Include applicable happy paths,
edge and validation boundaries, loading/pending, empty, error, unauthorized,
success, security, and regression behavior. Keep tests deterministic, isolated,
and focused on public behavior and contracts.

Tests should assert behavior and public contracts rather than private component
implementation details.

## Architecture Review Checklist

Before delivery, verify:

- Every new module has a clear owning layer or feature.
- Shared UI follows the Atomic Design hierarchy: atoms/molecules/organisms/
  templates exist under `components/` (or a feature's `components/` for
  feature-owned organisms), and no tier imports from a larger tier.
- Route files are thin and do not contain duplicated business rules.
- Server-only code cannot be imported by Client Components.
- Authentication and authorization are enforced server-side.
- Input validation and response error contracts are explicit.
- Database queries select appropriate fields and use safe transaction boundaries.
- Cache and revalidation behavior matches data sensitivity.
- Loading, empty, error, and pending UI states are implemented where relevant.
- Focused unit, integration, route, or end-to-end tests cover the risk introduced.
- Environment variables and setup commands are documented without exposing values.
