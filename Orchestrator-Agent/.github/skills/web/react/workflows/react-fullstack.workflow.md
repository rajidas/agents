# React Full-Stack Workflow

## Purpose

Coordinate a complete React single-page application through discovery, planning, implementation, integration, QA, and release validation.

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
`react-ui.agent.md`; Story Analysis to the orchestrator; Functional
Development to UI, API, and state workers; and QA gates to
`react-qa.agent.md`. Record analysis before UI generation. Use WCAG 2.0, 2.1,
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

Interpret clear requirements as values: a React ecommerce or React healthcare
management request supplies the application type and category; the same
applies to any clearly named domain. Named data sources, UI libraries, state
libraries, authentication methods, and feature/page names supply their
corresponding variables. Feature lists supply `MODULES`; do not ask for the
industry/category or modules again when they are already named. Ask only for
genuinely missing values.

When the prompt names a domain such as Ecommerce, Healthcare, Education,
Finance, Logistics, Hospital, SaaS, Portfolio, or Blog, store it as
`APPLICATION_TYPE` and skip that discovery question. Ask for application type
only when no domain can be inferred.

## Stages

1. **Planning**: `react-orchestrator.agent.md` analyzes requirements, route templates, dependencies, execution order, security, and acceptance criteria.
2. **API integration**: `react-api.agent.md` implements typed data-fetching clients, auth headers, errors, and tests.
3. **State layer**: `react-state.agent.md` implements Redux Toolkit, React Query, Zustand, or Context with tested transitions.
4. **UI layer**: `react-ui.agent.md` implements accessible responsive components, routes, and workflows.
5. **QA**: `react-qa.agent.md` runs focused tests, lint, typecheck, build, accessibility checks, and Playwright coverage.
6. **Delivery**: the orchestrator aggregates outputs, resolves integration gaps, confirms security and route coverage, and reports readiness.

## Baseline Acceptance

Every generated app has a responsive shell, linked navigation, meaningful content for the selected application type, typed data flow, loading/empty/error states, accessible controls, and no secrets in browser code. Do not leave generic dead routes or fake CRUD actions.

## Completion Gate

The workflow is incomplete if it only scaffolds a React project or installs a
UI library. After dependency installation, workers must implement the selected
application's routes, pages, components, hooks, state, data boundary,
interactions, and responsive accessible UI. For the ecommerce template this
includes products, categories, inventory, customers, orders, cart, checkout,
and reports. QA must run focused tests, lint, typecheck, and production build;
the orchestrator must report the implemented surface and any unresolved
failure before declaring delivery ready.
