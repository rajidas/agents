# Next.js Full-Stack Workflow

## Purpose

Orchestrates the complete lifecycle of a Next.js App Router application through specialized worker agents.

The workflow ensures proper planning, separation of concerns, quality validation, and secure implementation.

---

## Inputs

### Existing Project

- Next.js App Router project
- TypeScript
- Existing codebase
- Environment configuration
- Database configuration
- Feature request
- Acceptance criteria

### New Project

When creating a new application, the Orchestrator must first gather project requirements before scaffolding.

---

## Project Discovery Phase

For new applications, ask the user the enterprise discovery questions one by one
and wait for each answer before planning or scaffolding.

1. "What is your Next.js Application Number or Project Name?" Store as
	`PROJECT_ID`.
2. "Which application type do you want to build?" Store as
	`APPLICATION_TYPE`.
	Options: Ecommerce, Healthcare, Technology / SaaS, Portfolio, Blog, Other.
	This answer controls the domain-specific route map and page content.
3. "What type of data should be used?" Store as `DATA_TYPE`.
	Options: Static Pages Only, Mock JSON Data, Local Database, REST API,
	GraphQL API, Real Backend with Database.
4. "Select your industry/category" Store as `CATEGORY`.
	Options: Ecommerce, Hospital, Healthcare, Tech & Science, ERP, Management,
	Education, Finance, Real Estate, Logistics, Manufacturing, Travel, Food
	Delivery, SaaS, Custom Industry.
5. "Select UI Style" Store as `UI_STYLE`.
	Options: Modern SaaS, Corporate, Minimal, Material Design, Glassmorphism,
	Neumorphism, Enterprise Dashboard, Luxury Premium, Dark Mode, Custom.
6. "Select Technology Stack" Store as `TECH_STACK`.
	Options: Next.js + Tailwind CSS, Next.js + Shadcn UI, Next.js + Material UI,
	Next.js + Chakra UI, Next.js + Tailwind + Shadcn + Prisma, Full Enterprise
	Stack.
7. "Do you need authentication?" Store as `AUTH_TYPE`.
	Options: No Authentication, Email Login, JWT, NextAuth, Google Login,
	Microsoft Login, Multi-Role RBAC.
8. If the requested features include an admin area, ask "Which modules do you
	need?" Store as `MODULES`.

The workflow must not scaffold a generic application before these requirements
are understood.

Before scaffolding, require a fresh project name and destination path outside
the `Orchestrator-Agent` repository. Verify that the destination is empty or
newly created. Never run `create-next-app .` inside the agent repository or
overwrite an existing application; ask for another path instead.

After discovery, route implementation to the selected template only. Ecommerce
uses products, product details, cart, checkout, orders, and admin products.
Healthcare uses patients, patient details, appointments, reports, and admin.
Technology / SaaS uses features, pricing, dashboard, projects, and settings.
Portfolio uses work, project details, about, and contact. Blog uses posts, post
details, category pages, and about. Other requires a custom route map based on
the user's domain workflows. All selected routes must be implemented, linked,
and backed by typed dynamic data; never render Ecommerce for another choice.

After discovery, planning output must include Project Architecture, Folder
Structure, UI Wireframe, Component Tree, API Structure, Database Design,
TypeScript Types, Sample Pages, Dashboard Design, and Responsive Layout Plan.

---

## Default Application Standards

Unless explicitly disabled, new applications should include:

### Layout

- Header
- Footer
- Navigation Menu
- Responsive Layout
- Main Content Area

### User Experience

- Search Bar
- Filter Support
- Sorting Support
- Pagination
- Loading State
- Empty State
- Error State

### Accessibility

- Semantic HTML
- ARIA Support
- Keyboard Navigation

### Code Quality

- TypeScript
- ESLint
- App Router
- Reusable Components
- Feature-Based Structure

---

## Workflow Stages

### Stage 1 - Planning

Agent:

- nextjs-orchestrator.agent.md

Responsibilities:

- Analyze requirements
- Identify dependencies
- Determine project type
- Create execution plan
- Generate execution tokens
- Assign work to specialists

Output:

- Execution Plan
- Dependency Graph
- Task Assignments

---

### Stage 2 - Database Layer

Agent:

- nextjs-db.agent.md

Skills:

- architecture.skill.md
- prisma.skill.md

Responsibilities:

- Create Prisma schema
- Generate migrations
- Create repository layer
- Configure persistence

Output:

- Models
- Schema
- Migrations
- Data Access Layer

---

### Stage 3 - API Layer

Agent:

- nextjs-api.agent.md

Skills:

- architecture.skill.md
- api_route.skill.md

Responsibilities:

- Route Handlers
- Server Actions
- Authentication
- Validation
- Business Logic

Output:

- API Contracts
- Route Handlers
- Server Actions

---

### Stage 4 - User Interface Layer

Agent:

- nextjs-ui.agent.md

Skills:

- architecture.skill.md
- shadcn.skill.md
- ui_standards.skill.md

Responsibilities:

- Layouts
- Pages
- Components
- Navigation
- Header
- Footer
- Search
- Filters
- Sorting
- Responsive Design

Output:

- UI Components
- App Router Pages
- State Management

---

### Stage 5 - Integration

Agent:

- nextjs-orchestrator.agent.md

Responsibilities:

- Aggregate outputs
- Validate dependencies
- Verify application boundaries
- Confirm end-to-end integration

Output:

- Integrated Solution

---

### Stage 6 - Quality Assurance

Agent:

- nextjs-qa.agent.md

Skills:

- architecture.skill.md
- playwright.skill.md

Responsibilities:

- Unit Tests
- Integration Tests
- E2E Tests
- Accessibility Validation
- Regression Validation

Output:

- Test Suite
- QA Report

---

### Stage 7 - Release Validation

Agent:

- nextjs-qa.agent.md

Responsibilities:

- Execute validation gates
- Verify production readiness
- Verify security requirements

Output:

- Release Approval
- Validation Report

---

## Validation Gates

### Build Gate

Must Pass:

- TypeScript Compilation
- ESLint
- Production Build

---

### Database Gate

Must Pass:

- Schema Validation
- Migration Validation
- Seed Validation

---

### API Gate

Must Pass:

- Request Validation
- Error Handling
- Authorization Checks

---

### UI Gate

Must Pass:

- Responsive Rendering
- Accessibility Checks
- Search Functionality
- Filter Functionality
- Sorting Functionality

---

### QA Gate

Must Pass:

- Playwright Tests
- Smoke Tests
- Critical User Journeys

---

## Security Rules

Must Validate:

- No secrets in client components
- No Prisma Client imported into client components
- No database access in UI layer
- No environment variable leakage
- Proper Server Component boundaries
- Proper Client Component boundaries

---

## Completion Criteria

A feature or application is considered complete only when:

- All assigned worker agents finish successfully.
- QA validation passes.
- Release gates pass.
- Security checks pass.
- Integration validation passes.
- Delivery summary is generated.

---

## Final Delivery Summary

The Orchestrator must provide:

### Project Summary

- Project Type
- Features Implemented
- Database Used
- APIs Generated
- Pages Generated

### Quality Report

- Tests Generated
- Validation Results
- Build Status

### Security Report

- Client/Server Boundary Validation
- Environment Variable Validation
- Persistence Layer Validation

Only produce the final delivery summary after all QA and release gates have passed.