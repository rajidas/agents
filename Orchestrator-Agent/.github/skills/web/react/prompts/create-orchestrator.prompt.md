# Create React Orchestrator

Create or update the React orchestrator using `agents/react-orchestrator.agent.md` and the architecture, UI, API, state, and testing skills in this package.

## Input Classification and Flow

Classify the request as User Prompt, Figma Link / MCP Design, Design Screenshot
/ UI Image, Jira User Story, Azure DevOps Story, Bug Ticket, Enhancement
Request, or Existing Application Code. Then follow:

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

Map the first three analysis/layout stages to `REACT_UI_AGENT`, Story
Analysis to the orchestrator, Functional Development to the React worker
agents, and QA stages to `REACT_QA_AGENT`. Record analysis before UI work.
Use WCAG 2.0, 2.1, and 2.2 by default with WCAG 2.2 Level AA as the target;
reuse the existing compatible test runner and use Playwright for browser flows.

## Discovery HARD GATE

Before asking anything, parse the complete current prompt and conversation.
Store every explicit or unambiguous natural-language value already supplied.
Never repeat an answered question. Ask only the first genuinely missing value,
one question per turn. If all required values are supplied, ask no discovery
questions and continue to planning, scaffolding, implementation, and QA.

"React ecommerce" supplies both application type and category. The same
applies to "React healthcare management", "React education platform", and
any clearly named domain such as finance, logistics, hospital, SaaS, portfolio,
or blog. Named data sources, UI libraries, state libraries, authentication
methods, and features supply their corresponding values. Do not ask for those
values again, including the industry/category question.

Do not ask "Which application type..." when the prompt names Ecommerce,
Healthcare, Education, Finance, Logistics, Hospital, SaaS, Portfolio, Blog, or
another clear application domain. Store the named domain as `APPLICATION_TYPE`
and continue with the first missing value only.

Before generating code, architecture, routes, components, hooks, schemas, or UI,
ask missing questions one at a time and wait after each answer:

1. React application number or project name: `PROJECT_ID`.
2. Application type: Ecommerce, Healthcare, Technology / SaaS, Portfolio, Blog, or Other: `APPLICATION_TYPE`.
3. Data type: Static Pages Only, Mock JSON Data, REST API, GraphQL API, Local Database, or Real Backend with Database: `DATA_TYPE`.
4. Industry/category: Ecommerce, Hospital, Healthcare, ERP, Education, Finance, Logistics, SaaS, Manufacturing, Real Estate, Management, Travel, Food Delivery, or Custom Industry: `CATEGORY`.
5. UI style: Modern SaaS, Corporate, Minimal, Material Design, Glassmorphism, Enterprise Dashboard, Dark Mode, or Custom: `UI_STYLE`.
6. Technology stack: React + Tailwind CSS, React + Material UI, React + Chakra UI, React + Tailwind + Redux Toolkit, React + shadcn/ui, or Full Enterprise Stack: `TECH_STACK`.
7. Authentication: No Authentication, JWT, OAuth2, Google Login, Microsoft Login, or Multi-Role RBAC: `AUTH_TYPE`.
8. State management: Redux Toolkit, React Query / TanStack Query, Zustand, or Context Only: `STATE_MANAGEMENT`.
9. When an admin area is required, ask for modules and store `MODULES`.

Do not use defaults without explicit confirmation. Require `PROJECT_NAME` and `PROJECT_PATH` outside the `Orchestrator-Agent` repository before scaffolding. The destination must be empty or newly created; never scaffold into this repository or overwrite an existing app.

## Required Output

Return the project overview, requirements, roles, React architecture, folder structure, route tree, component tree, hooks, state design, API contracts, auth design, UI plan, testing strategy, deployment guide, dependency graph, worker assignments, execution order, acceptance criteria, security guardrails, and validation commands. Route implementation to the smallest capable React worker and forward all completed work to QA.
