# Next.js Agent Kit

A simple VS Code Copilot setup for creating and extending Next.js App Router
applications with TypeScript, Tailwind CSS, accessibility, testing, API, and
database guidance.

## Files

### Agents

| File | Purpose |
|---|---|
| 
extjs-orchestrator.agent.md` | Collects requirements and coordinates the work. |
| 
extjs-ui.agent.md` | Creates pages, layouts, header, footer, navigation, search, and filters. |
| 
extjs-api.agent.md` | Creates secure route handlers and server mutations. |
| 
extjs-db.agent.md` | Creates Prisma models, migrations, and server-only data access. |
| 
extjs-qa.agent.md` | Runs tests, accessibility checks, builds, and release validation. |

### Prompts

| File | Purpose |
|---|---|
| `create-orchestrator.prompt.md` | Creates the execution plan and asks for app requirements. |
| `scaffold-app.prompt.md` | Creates a new Next.js application skeleton. |
| `ui-task.prompt.md` | Implements a UI change. |
| `api-task.prompt.md` | Implements an API or server mutation. |
| `qa-task.prompt.md` | Tests and validates a Next.js change. |

### Skills

| File | Purpose |
|---|---|
| `architecture.skill.md` | Defines project structure and server/client boundaries. |
| `ui_standards.skill.md` | Defines responsive, accessible UI standards. |
| `api_route.skill.md` | Defines route validation, responses, errors, and security. |
| `prisma.skill.md` | Defines schema, migrations, queries, and transactions. |
| `playwright.skill.md` | Defines browser tests, fixtures, and user-flow coverage. |
| `shadcn.skill.md` | Defines optional shadcn/ui usage and accessibility rules. |

### Workflow

| File | Purpose |
|---|---|
| 
extjs-fullstack.workflow.md` | Defines the full order from planning through QA and release. |

---

## Setup

Copy this Next.js `.github` setup into the root of your project:

```
your-project/
  .github/
    agents/
    prompts/
    skills/
    workflows/
```

Requires VS Code + GitHub Copilot Chat with support for custom agents
(`*.agent.md`) and prompt files (`*.prompt.md`). If your VS Code build only has
the older "chat modes" naming, rename 
extjs-orchestrator.agent.md` to

extjs-orchestrator.chatmode.md` - the content works either way.

### Figma MCP

The workspace includes `.vscode/mcp.json` with the official Figma remote MCP
server at `https://mcp.figma.com/mcp`. In VS Code, start or authenticate the
`figma` MCP server when prompted, then provide the UI agent with a Figma file
URL containing a 
ode-id` (or an explicit node ID). The agent inspects the
target node, maps it to existing components and tokens, retrieves assets through
MCP, and validates the implementation with rendered browser screenshots when
browser tooling is available.

Open the Next.js application as the active workspace before implementation. If
the workspace only contains this agent kit, the UI agent must obtain approval
for a separate project directory and scaffold there; it must not create an app
inside this repository.

## Use the Agent

1. Open Copilot Chat.
2. Select **Next.js Orchestrator Agent** from the agent dropdown.
3. Use `/create-orchestrator` for a new project.
4. Use `/scaffold-app` to add models and CRUD to an existing project.
5. Approve terminal commands when Copilot asks to run npm, Next.js, Prisma, or
  test commands.


## Create New Application Step 

Use `/create-orchestrator` or ask Copilot to create a new Next.js application.

The agent asks the enterprise discovery questions one by one before generating
architecture or code:

- Next.js application number or project name (`PROJECT_ID`)
- Application type (`APPLICATION_TYPE`)
- Data type (`DATA_TYPE`)
- Industry/category (`CATEGORY`)
- UI style (`UI_STYLE`)
- Technology stack (`TECH_STACK`)
- Authentication type (`AUTH_TYPE`)
- Dashboard modules (`MODULES`) when the selected app type includes an admin
  dashboard

After discovery, the agent produces the full enterprise output set: project
architecture, folder structure, UI wireframe, component tree, API structure,
database design, TypeScript types, sample pages, dashboard design, and
responsive layout plan.

### Landing Page

Creates a responsive public page with:

- Header and footer with example content
- Category navigation and filters
- Blog post listing and detail pages
- Functional search with URL parameters
- Loading, empty, error, and reset states

### Admin Dashboard

Creates protected management screens with:

- Category and blog post list pages
- Create, view, edit, and delete CRUD flows
- Validation, authorization, pending, error, and confirmation states

When both modes are selected, public and admin routes share validated models,
services, and persistence while keeping admin mutations protected.

## Add Data

Use `/scaffold-app` when a project needs new entities. It can add Prisma models,
migrations, typed server-only data access, API routes, and starter UI. It must
not add Prisma, authentication, or UI libraries unless requested.

## Run the App

```bash
cd your-project
npm run dev
```

Open `http://localhost:3000`.

## Agentic SDLC Validation

The kit validates a new application through eight evidence-backed gates. The orchestrator owns G1-G3 and coordinates the remaining gates; 
extjs-qa.agent.md` independently validates G4-G8. A missing, skipped, unavailable, or failing critical check is `BLOCKED`, never `PASS`.

| Gate | Validate | Evidence location |
|---|---|---|
| G1 Framework | Next.js App Router, TypeScript, styling, package manager, repository rules, and external project path | Framework Report and Tech Stack Document |
| G2 Input | Prompt, Figma/story, scope, assumptions, and acceptance criteria | Input Summary and Acceptance Criteria |
| G3 Design | Wireframe, component map, root cause, blast radius, dependencies, and affected flows | Design/Story Analysis and Component Map |
| G4 Development | TypeScript, lint, unit tests, API boundaries, configuration, and build | Test output and implementation handoff |
| G5 QA | Integration, Playwright, accessibility, responsive, browser, and critical user journeys | Test Cases, Execution Results, Accessibility/Coverage Reports |
| G6 Review | Static analysis, dependency/secret scans, authorization, server/client boundaries, and regressions | Review, Security, and Regression Reports |
| G7 Release | End-to-end smoke flows, screenshots/logs, rollback, and production readiness | Validation Report and RFQA Evidence Package |
| G8 Learning | Defects, root causes, lessons, new tests, patterns, guidelines, and automation | Knowledge Base Update and Retrospective Notes |

### Where to store project evidence

For each generated application, keep evidence in the application repository:

```text
docs/requirements-baseline.md
docs/acceptance-criteria.md
docs/design-analysis.md
docs/traceability-matrix.md
docs/dependency-graph.md
docs/risk-register.md
docs/validation-report.md
docs/rfqa-package.md
docs/lessons-learned.md
tests/unit/
tests/integration/
tests/e2e/
```

### Commands to run

Run these from the generated Next.js application directory. Use the scripts available in its `package.json`:

```bash
npm run lint
npx tsc --noEmit
npm test
npm run build
npx playwright test
```

Record the command, exit status, affected flow, acceptance criterion, and artifact path in the evidence record. Do not approve release until every acceptance criterion traces to an executed check and evidence artifact.

## Notes

- Keep feature code separate from routes, UI, and persistence.
- Keep secrets and database clients on the server.
- Use the existing project conventions before adding dependencies.
- Run lint, typecheck, build, and focused tests before delivery.
