# Next.js Agent Kit

A simple VS Code Copilot setup for creating and extending Next.js App Router
applications with TypeScript, Tailwind CSS, accessibility, testing, API, and
database guidance.

## Files

### Agents

| File | Purpose |
|---|---|
| `nextjs-orchestrator.agent.md` | Collects requirements and coordinates the work. |
| `nextjs-ui.agent.md` | Creates pages, layouts, header, footer, navigation, search, and filters. |
| `nextjs-api.agent.md` | Creates secure route handlers and server mutations. |
| `nextjs-db.agent.md` | Creates Prisma models, migrations, and server-only data access. |
| `nextjs-qa.agent.md` | Runs tests, accessibility checks, builds, and release validation. |

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
| `nextjs-fullstack.workflow.md` | Defines the full order from planning through QA and release. |

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
the older "chat modes" naming, rename `nextjs-orchestrator.agent.md` to
`nextjs-orchestrator.chatmode.md` - the content works either way.

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

## Notes

- Keep feature code separate from routes, UI, and persistence.
- Keep secrets and database clients on the server.
- Use the existing project conventions before adding dependencies.
- Run lint, typecheck, build, and focused tests before delivery.
