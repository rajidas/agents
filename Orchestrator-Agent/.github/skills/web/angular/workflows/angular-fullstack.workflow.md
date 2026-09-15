# Angular Full-Stack Workflow

## Purpose

Coordinate a complete Angular application through discovery, planning, implementation, integration, QA, and release validation.

Use the latest stable Angular and matching CLI by default. If the user names an
Angular version, honor it after checking toolchain and dependency compatibility;
record the selected Angular and CLI versions before scaffolding.

## Input Classification and Flow

Classify each request as User Prompt, Figma Link / MCP Design, Design
Screenshot / UI Image, Jira User Story, Azure DevOps Story, Bug Ticket,
Enhancement Request, or Existing Application Code. Existing-app changes, bugs,
enhancements, and refactors bypass new-project discovery.

```text
Select Input Source -> source-specific analysis -> UI Layout Generation Agent
User Prompt -> Atomic Design Pattern Agent
Figma / Image -> Design Analysis Agent
User Story -> Story Analysis Agent
						 -> Functional Development Agent
						 -> Accessibility Validation
						 -> Test Case Generation Agent
						 -> Automated Unit / Integration Tests
						 -> Cross-Browser & Responsive Testing
```

Map Atomic Design, Design Analysis, and UI Layout Generation to
`angular-ui.agent.md`; Story Analysis to the orchestrator; Functional
Development to UI, app, API, state, and DB workers; and QA gates to
`angular-qa.agent.md`. Record analysis before UI generation. Use WCAG 2.0, 2.1,
and 2.2 by default, targeting WCAG 2.2 Level AA. Inspect and reuse the
compatible project test runner, then use Playwright for browser journeys.

## Discovery Gate

For new apps first parse the complete current prompt and conversation. Collect
`PROJECT_ID`, `APPLICATION_TYPE`, `DATA_TYPE`, `CATEGORY`, `UI_STYLE`,
`TECH_STACK`, `AUTH_TYPE`, `STATE_MANAGEMENT`, applicable `MODULES`,
`PROJECT_NAME`, and `PROJECT_PATH` from information already supplied. Ask only
the first genuinely missing question, one per turn; never repeat an answered
question. If all values are present, skip discovery and proceed to destination
validation, scaffolding, implementation, and QA.

Interpret clear requirements as values: an Angular ecommerce or Angular
healthcare management request supplies the application type and category; the
same applies to any clearly named domain. Named data sources, UI libraries,
state libraries, authentication methods, and feature/page names supply their
corresponding variables. Feature lists supply `MODULES`; do not ask for the
industry/category or modules again when they are already named. Ask only for
genuinely missing values.

When the prompt names a domain such as Ecommerce, Healthcare, Education,
Finance, Logistics, Hospital, SaaS, Portfolio, or Blog, store it as
`APPLICATION_TYPE` and skip that discovery question. Ask for application type
only when no domain can be inferred.

## Stages

1. **Planning**: `angular-orchestrator.agent.md` analyzes requirements, route templates, dependencies, execution order, security, and acceptance criteria.
2. **Data and contracts**: `angular-db.agent.md` defines DTOs, domain models, mapping, pagination, and backend coordination.
3. **API integration**: `angular-api.agent.md` implements typed HttpClient services, interceptors, auth, errors, and tests.
4. **Application layer**: `angular-app.agent.md` implements standalone routes, lazy loading, guards, layouts, providers, and feature services.
5. **State layer**: `angular-state.agent.md` implements Signals, RxJS, or the selected store with tested transitions.
6. **UI layer**: `angular-ui.agent.md` implements accessible responsive components and workflows.
7. **QA**: `angular-qa.agent.md` runs focused tests, lint, typecheck, build, accessibility checks, and Playwright coverage.
8. **Delivery**: the orchestrator aggregates outputs, resolves integration gaps, confirms security and route coverage, and reports readiness.

## Baseline Acceptance

Every generated app has a responsive shell, linked navigation, meaningful content for the selected application type, typed data flow, loading/empty/error states, accessible controls, and no secrets in browser code. Do not leave generic dead routes or fake CRUD actions.

## Completion Gate

The workflow is incomplete if it only scaffolds Angular or installs Angular
Material. After dependency installation, workers must implement the selected
application's routes, pages, components, services, state, data boundary,
interactions, and responsive accessible UI. For the ecommerce template this
includes products, categories, inventory, customers, orders, cart, checkout,
and reports. QA must run focused tests, lint, typecheck, and production build;
the orchestrator must report the implemented surface and any unresolved
failure before declaring delivery ready.
