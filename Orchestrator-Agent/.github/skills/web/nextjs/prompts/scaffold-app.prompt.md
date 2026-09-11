# Scaffold Next.js App

Scaffold a production-ready Next.js App Router TypeScript application.

Apply `skills/architecture.skill.md` and `skills/ui_standards.skill.md` for the
base structure. Apply `skills/shadcn.skill.md` only when shadcn/ui is already
configured or explicitly requested. Apply `skills/prisma.skill.md` only when a
database is requested. Include an accessible root layout, home route, health
route, environment sample, lint/format baseline, and documented install,
development, test, and build commands.

## Required Discovery Questions — HARD GATE

This gate overrides any other instruction in this prompt, including the
scaffold and structure sections below. Do not run `create-next-app`, do not
generate any project files, folders, or plans, and do not apply any skill until
this gate is satisfied.

Ask the enterprise discovery questions one at a time, in the order listed.
Stop and wait for the user's reply after each question before asking the next
one. Never batch questions together and never assume an answer, even a
seemingly obvious one:

1. What is your Next.js Application Number or Project Name? Store as
	`PROJECT_ID`.
2. Which application type do you want to build? Store as `APPLICATION_TYPE`.
	Options: Ecommerce, Healthcare, Technology / SaaS, Portfolio, Blog, Other.
	This answer selects the domain-specific route and page template.
3. What type of data should be used? Store as `DATA_TYPE`.
	Options: Static Pages Only, Mock JSON Data, Local Database, REST API,
	GraphQL API, Real Backend with Database.
4. Select your industry/category. Store as `CATEGORY`.
	Options: Ecommerce, Hospital, Healthcare, Tech & Science, ERP, Management,
	Education, Finance, Real Estate, Logistics, Manufacturing, Travel, Food
	Delivery, SaaS, Custom Industry.
5. Select UI Style. Store as `UI_STYLE`.
	Options: Modern SaaS, Corporate, Minimal, Material Design, Glassmorphism,
	Neumorphism, Enterprise Dashboard, Luxury Premium, Dark Mode, Custom.
6. Select Technology Stack. Store as `TECH_STACK`.
	Options: Next.js + Tailwind CSS, Next.js + Shadcn UI, Next.js + Material UI,
	Next.js + Chakra UI, Next.js + Tailwind + Shadcn + Prisma, Full Enterprise
	Stack.
7. Do you need authentication? Store as `AUTH_TYPE`.
	Options: No Authentication, Email Login, JWT, NextAuth, Google Login,
	Microsoft Login, Multi-Role RBAC.
8. If the requested features include an admin area, ask which modules are
	needed and store as `MODULES`.

Never silently choose an application mode, data mode, UI style, technology
stack, authentication type, or dashboard modules. Do not start coding until all
applicable answers are collected. If the user says "just create it" or "use
defaults", still ask each remaining question, optionally suggesting a default,
and get explicit confirmation before storing it.

## Application-Type Routing Contract

The selected application type controls the generated route map and page content.
Never hard-code Ecommerce as the default. Implement and link these base routes:

- Ecommerce: `/products`, `/products/[slug]`, `/cart`, `/checkout`, `/orders`,
	and `/admin/products`.
- Healthcare: `/patients`, `/patients/[id]`, `/appointments`, `/reports`, and
	`/admin`.
- Technology / SaaS: `/features`, `/pricing`, `/dashboard`, `/projects`, and
	`/settings`.
- Portfolio: `/work`, `/work/[slug]`, `/about`, and `/contact`.
- Blog: `/blog`, `/blog/[slug]`, `/categories/[slug]`, and `/about`.
- Other: ask for the domain workflows and create a documented custom map.

Use typed dynamic data and relevant working interactions on every selected map.
Include loading, empty, error, search, filter, and sort states where relevant.
Do not create dead or generic placeholder routes.

# External Fresh Project Location — Required

Create every new Next.js application outside the `Orchestrator-Agent`
repository in a fresh, user-named folder. Ask for the project name and
destination path before scaffolding. Verify the destination is outside the
agent repository and empty or newly created. Never run `create-next-app .`
inside the agent repository, reuse an existing app folder, or overwrite files.
If the destination is not empty, ask for another path.

# Next.js Application Scaffold Standards

When creating a new Next.js application, always include:

## Layout

- Responsive Header
- Footer
- Navigation Menu
- Main Content Area

## Features

- Search Bar with filter and sort controls
- Loading States
- Error Boundary
- Empty State Handling

## Category Navigation Requirement

Regardless of `APPLICATION_TYPE` or `CATEGORY`, the header must expose a
category/section navigation menu and the footer must expose its own
footer-category navigation (e.g., Company, Support, Legal, Resources). When
`CATEGORY` is `Custom Industry` or `APPLICATION_TYPE` is `Custom Application`,
invent a plausible, clearly labeled set of categories and at least two unique
sections (e.g., featured items rail, stats band, FAQ) instead of producing a
generic, unthemed skeleton.

## UI

- Tailwind CSS
- shadcn/ui components only when selected or already installed
- Mobile Responsive Design

## Structure

app/
components/
  atoms/
  molecules/
  organisms/
  templates/
lib/
hooks/
services/

## Accessibility

- Semantic HTML
- Keyboard Navigation
- ARIA Labels
- Focus Management

# Application Bootstrap Rules

Never generate a raw create-next-app output.

After project creation, inspect the generated files and immediately scaffold:

- Shared responsive `Header` and `Footer`
- Main `Navigation` with valid example routes
- Accessible `Search Bar` example
- Example landing page with a heading, description, primary action, and CTA
- At least three reusable `Feature Card` components
- Skip-to-content link and semantic main content landmark

When `Landing page` is selected, also scaffold:

- Public category navigation and category filter
- Public blog post listing with functional search, sorting, and pagination-ready
	state
- Blog post detail route with published-content handling
- Typed example content source that is easy to replace with an API or database
- URL-persisted search and category parameters that change visible results
- Accessible clear/reset control and visible loading, empty, invalid-query, and
	recoverable error states

When `Admin dashboard` is selected, also scaffold:

- Protected `/admin` dashboard with navigation
- Category and blog post management screens
- Create, list, view, edit, and delete CRUD flows
- Validation, loading, empty, error, pending, and delete-confirmation states

When `Both` is selected, scaffold both sets of routes and connect them to the
same server-side services, repositories, and validated data models. Public pages
must expose published content only; admin mutations must require authentication
and authorization.

Default Pages for all modes:

/
/about
/contact

Additional mode-dependent pages:

/categories
/blog
/blog/[slug]
/admin
/admin/categories
/admin/posts

Include:

- Responsive design
- Tailwind CSS
- TypeScript
- App Router

Before implementation, produce the required enterprise plan: project overview,
business requirements, user roles, information architecture, folder structure,
App Router structure, page list, admin dashboard pages when relevant, API
design, database schema, component structure, UI layout, Tailwind setup,
shadcn/ui component plan, state management, mock JSON data, TypeScript
interfaces, SEO plan, authentication flow, deployment guide, UI wireframe,
component tree, sample pages, dashboard design, and responsive layout plan.

## Architecture and Quality Rules

- Use `src/` when selected by the project and keep the same boundaries without
	it when not selected.
- Keep routes thin; place business rules in feature services and persistence in
	server-only repositories.
- Keep secrets, database clients, and private environment variables on the
	server. Add a safe environment sample without real values.
- Include loading, error, empty, pending, and not-found handling where relevant.
- Use semantic HTML, keyboard navigation, accessible labels, focus management,
	and responsive layouts.
- Do not leave the default starter page in the final solution.
- Do not add fake CRUD buttons without a working persistence or clearly isolated
	mock repository selected by the user.
- Keep public content reads separate from protected admin mutations.
- Validate all category and blog post input and enforce admin authorization on the
	server for every mutation.
- Use a shared `Category` and `BlogPost` model with a deliberate relationship,
	publication status, slug uniqueness, and indexes for search/filter queries.
- Add focused smoke or unit coverage when the configured test tooling exists.

## Ready-to-Use Skeleton Acceptance Criteria

- `app/layout.tsx` renders the shared header before and footer after page content.
- The home page contains replaceable dummy content, not a blank or raw starter
	screen.
- Header and footer are responsive and usable on mobile and desktop.
- Navigation links point to implemented example routes or clearly documented
	placeholder destinations.
- The search example has an accessible label and does not require a backend.
- Landing-page search changes the visible blog post results and preserves its
	query in the URL.
- Landing-page category filters change visible results and preserve the selected
	category in the URL.
- Search and filtering are keyboard accessible and include a clear/reset action.
- Feature cards are reusable components, not repeated inline markup.
- The skeleton passes lint, typecheck, and production build validation.

## Completion Report

Return the created structure, routes, dependencies added, setup commands, test
commands, validation results, and any assumptions or intentionally omitted
features. Do not claim a database, authentication, or UI library was added
unless it was requested and configured.