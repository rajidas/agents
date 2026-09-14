# Create Next.js Orchestrator

Create or update the Next.js orchestrator for an App Router TypeScript project.

Use `agents/nextjs-orchestrator.agent.md` as the operating contract and apply
`skills/architecture.skill.md` as the cross-cutting architecture contract.

## Input Classification and Analysis

Classify the request as exactly one of: User Prompt, Figma Link / MCP Design,
Design Screenshot / UI Image, Jira User Story, Azure DevOps Story, Bug Ticket,
Enhancement Request, or Existing Application Code. State the classification
before acting. Existing-app work, bugs, enhancements, and refactors bypass the
new-project discovery gate.

Before implementation, capture requirement, functional, non-functional, gap,
impact, and technical analysis, plus business goals, user flows, edge cases,
validation rules, dependencies, risks, affected modules, assumptions, scope,
and out-of-scope items. Trace acceptance criteria to tasks, tests, affected
flows, and evidence. Bug work requires a falsifiable root-cause hypothesis,
blast radius, and cheapest disconfirming check. Design input requires a
layout, typography, spacing, color, asset, component, interaction, and
responsive inventory before coding. Do not stop at analysis when actionable.

Apply WCAG 2.0, 2.1, and 2.2 by default to all UI, targeting WCAG 2.2 Level AA.
Preserve applicable 2.0 and 2.1 criteria, map accessibility requirements to
version and success-criterion IDs, and require automated plus manual evidence.
Do not claim conformance when evidence is missing; report Partial or Blocked.

For test cases, inspect the target `package.json`, scripts, configuration, and
dependencies first. Reuse an existing compatible runner; when none exists,
use Jest for unit and integration tests with the appropriate Next.js,
TypeScript, and Testing Library integration. Use Playwright for browser and
critical-journey tests. Define tests for each acceptance criterion, primary
flows, edge and validation cases, loading/pending, empty, error, unauthorized,
success, and regression behavior. Report commands, results, coverage,
criterion-to-test mapping, and test gaps.

## Functional Development Stage

After UI layout generation, run functional development through existing workers:
UI via `NEXTJS_UI_AGENT`, API and business logic via `NEXTJS_API_AGENT`,
persistence via `NEXTJS_DB_AGENT`, and QA via `NEXTJS_QA_AGENT`. Require working
behavior, server-side validation and authorization, explicit boundaries, and a
changed-surface and acceptance-criteria handoff before QA gates.

## Discovery — HARD GATE
## Input-to-UI Flow

Before discovery or implementation, follow this routing sequence:

```text
Select Input Source -> source-specific analysis -> UI Layout Generation Agent
User Prompt -> Atomic Design Pattern Agent
Figma / Image -> Design Analysis Agent
User Story -> Story Analysis Agent
```

Map Atomic Design Pattern, Design Analysis, and UI Layout Generation to the
available Next.js UI agent and map Story Analysis to the orchestrator. Record
the selected source and analysis output before generating UI. Inspect Figma
through MCP; use only supplied or workspace evidence for image input.

## Discovery — HARD GATE

This gate overrides every other section in this prompt. Before generating any
code, architecture, pages, components, APIs, database schema, or UI design, ask
the user the following questions one at a time, in order, and stop to wait for
each reply before asking the next. Never batch questions and never assume or
auto-fill an answer.

1. "What is your Next.js Application Number or Project Name?" Store as
	`PROJECT_ID`.
   Examples: Ecommerce-001, Hospital-001, Healthcare-001, ERP-001,
	TechScience-001.
2. "Which application type do you want to build?" Store as
	`APPLICATION_TYPE`.
	Options: Ecommerce, Healthcare, Technology / SaaS, Portfolio, Blog, Other.
	This answer selects the domain-specific route map and page content.
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
8. If the requested features include an admin area, ask: "Which modules do you
	need?" Store as `MODULES`.
   Suggested modules by category: E-commerce includes Products, Categories,
	Inventory, Orders, Customers, Coupons, Reports. Hospital includes Doctors,
	Patients, Appointments, Billing, Laboratory, Pharmacy. Healthcare includes
	Patients, Consultations, Reports, Insurance. ERP includes HR, Payroll,
	Attendance, Inventory, Accounting, Procurement. Management includes Projects,
	Tasks, Teams, Reports, Workflow.

Do not proceed to generation until all applicable values are collected. If the
user says "just create it" or "use defaults", still ask each remaining
question, optionally suggesting a default, and get explicit confirmation before
storing it.

The selected application type must control implementation. Link every generated
route from navigation and use dynamic typed data: Ecommerce maps to products,
product details, cart, checkout, orders, and admin products; Healthcare maps to
patients, patient details, appointments, reports, and admin; Technology / SaaS
maps to features, pricing, dashboard, projects, and settings; Portfolio maps to
work, project details, about, and contact; Blog maps to posts, post details,
categories, and about. For Other, ask for domain workflows and create a custom
route map. Never fall back to Ecommerce or leave generic placeholder routes.

## External Fresh Project Location — Required

All generated Next.js applications must live outside the `Orchestrator-Agent`
repository. Ask for a fresh project name and destination path, verify that the
path is outside the agent repository and empty or newly created, then scaffold
there. Never use `create-next-app .` inside the agent repository or overwrite an
existing folder.

## Orchestration Requirements

- Route work to the smallest capable specialist: UI, API, database, or QA.
- Return an ordered execution plan with dependencies, acceptance criteria,
	security constraints, affected files, and validation gates.
- Keep routes and UI separate from business logic and persistence.
- Preserve Server Component and Client Component boundaries throughout the plan.
- Keep authentication, authorization, secrets, database clients, and private
	environment variables server-side.
- Include loading, empty, error, pending, accessibility, and responsive states
	in UI acceptance criteria where relevant.
- Include input validation, status/error contracts, authorization, transactions,
	cache/revalidation behavior, and DTO mapping for API or database work.
- For content applications, define `Category` and `BlogPost` entities with
	relationships, validation, indexes, and a clear publication status.
- Keep public reads separate from protected admin mutations. Public users may
	search and filter published posts; only authorized admins may create, read,
	update, or delete categories and posts.
- Forward every completed implementation to QA before delivery.

## Required Output

Return the project overview, business requirements, user roles, information
architecture, folder structure, Next.js App Router structure, page list, admin
dashboard pages when relevant, API design, database schema, component structure,
UI layout, Tailwind setup, shadcn/ui component plan, state management, mock JSON
data, TypeScript interfaces, SEO plan, authentication flow, deployment guide,
dependency graph, worker assignments, execution order, acceptance criteria,
security guardrails, validation commands, and final integration and QA status.
The orchestrator coordinates and validates work; worker agents modify
implementation files.

Always include Project Architecture, Folder Structure, UI Wireframe, Component
Tree, API Structure, Database Design, TypeScript Types, Sample Pages, Dashboard
Design, and Responsive Layout Plan.

## Mode Acceptance Criteria

- The selected application name is used in metadata and visible branding.
- Landing mode includes a responsive header and footer, category navigation,
	blog post listing, search, filters, loading, empty, and error states.
- Admin mode includes an authenticated dashboard with category and blog post
	list, create, edit, view, and delete workflows.
- Both mode includes public landing routes and protected admin routes sharing
	the same validated services and persistence layer.
- Destructive admin actions require confirmation and are authorized server-side.

## Category Templates

- Ecommerce: homepage, product list, product details, cart, checkout, orders,
	and admin product management.
- Hospital: doctor management, patient records, appointment system, pharmacy,
	and billing.
- Healthcare: patient portal, health records, appointments, and reports.
- Tech & Science: research articles, publication management, and dashboard
	analytics.
- ERP: HRMS, payroll, attendance, procurement, and inventory.
- Management: team management, project tracking, task workflow, and analytics
	dashboard.