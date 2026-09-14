# Next.js Agent Kit: Demo Guide

## One-line explanation

This kit turns one Next.js request into an ordered workflow: understand the
requirements, build each layer with the right specialist, then validate the
finished application.

## What to say in a demo

> "The Next.js Orchestrator is the coordinator. It first collects the project
> requirements one at a time, then routes UI, API, database, and test work to
> focused agents. The worker agents use shared skills for consistent standards,
> and QA checks the result before release."

## File map

| File or folder | What it does | Why it is used |
| --- | --- | --- |
| `../../../agents/NEXTJS_AGENT.agent.md` | Main Next.js router. Runs discovery, creates the plan, delegates work, integrates results, and owns delivery. | Prevents one agent from mixing planning, coding, and testing responsibilities. |
| `agents/nextjs-orchestrator.agent.md` | The detailed coordinator contract used inside the Next.js kit. | Defines the same workflow close to its specialists, prompts, and skills. |
| `agents/nextjs-ui.agent.md` | Builds pages, layouts, navigation, responsive UI, search, filters, and accessible states. | Keeps visual and interaction work consistent with App Router and accessibility rules. |
| `agents/nextjs-api.agent.md` | Builds route handlers, Server Actions, authentication, validation, and business logic. | Keeps requests secure and prevents server-only logic from leaking into UI code. |
| `agents/nextjs-db.agent.md` | Builds Prisma schemas, migrations, relations, indexes, and server-only data access. | Makes database changes typed, reproducible, and isolated from the browser. |
| `agents/nextjs-qa.agent.md` | Adds and runs unit, integration, Playwright, accessibility, build, lint, and type checks. | Provides an independent release gate rather than assuming implementation is correct. |
| `prompts/create-orchestrator.prompt.md` | Starts a new project conversation and gathers requirements. | Gives the coordinator a repeatable discovery script. |
| `prompts/scaffold-app.prompt.md` | Creates a production-ready app foundation after discovery. | Replaces the default starter page with a usable, structured application. |
| `prompts/ui-task.prompt.md` | Performs a focused UI change. | Applies UI architecture and accessibility standards to a single feature. |
| `prompts/api-task.prompt.md` | Performs a focused API or server-mutation change. | Enforces validation, authorization, stable responses, and server boundaries. |
| `prompts/qa-task.prompt.md` | Validates a focused change. | Reuses the test strategy without repeating it in each task. |
| `skills/architecture.skill.md` | Defines the App Router structure, component boundaries, and server/client separation. | Gives every specialist one shared technical architecture. |
| `skills/ui_standards.skill.md` | Defines responsive and accessible UI behavior. | Makes UI output consistent and usable across screen sizes. |
| `skills/api_route.skill.md` | Defines input validation, error handling, status codes, and API security. | Makes endpoints predictable and safe. |
| `skills/prisma.skill.md` | Defines Prisma schema, migration, query, and transaction rules. | Keeps persistence changes safe and repeatable. |
| `skills/playwright.skill.md` | Defines browser-test structure, fixtures, and critical user-flow coverage. | Tests the application as a user experiences it. |
| `skills/shadcn.skill.md` | Defines optional shadcn/ui usage. | Allows reusable accessible components only when that library is selected. |
| `workflows/nextjs-fullstack.workflow.md` | States the complete order: planning, database, API, UI, integration, QA, and release validation. | Makes dependencies explicit; for example, UI can rely on stable data and API contracts. |
| `workflows/copilot-instructions.md` | Always-on repository rules and agent-routing table. | Ensures the correct specialist is selected even when the default chat agent is active. |
| `README.md` | Setup and quick-start reference for the whole kit. | Gives a new user one place to understand installation and usage. |

## Demo flow

1. Select **Next.js Orchestrator** in Copilot Chat.
2. Run `/create-orchestrator` for a new application.
3. The agent asks one requirement at a time: project name, application type,
   data source, industry, UI style, stack, authentication, and admin modules.
4. It creates the plan and routes work in order: database, API, UI,
   integration, then QA.
5. QA runs focused tests, lint, typecheck, build, Playwright, and accessibility
   checks before the work is considered ready.

## How to demonstrate validation

After implementation, show the gate matrix instead of only showing generated code:

1. **G1-G2: Confirm the baseline.** Show the detected stack, project location, input source, scope, assumptions, and acceptance criteria.
2. **G3: Approve understanding.** Show the wireframe/component map, root-cause hypothesis, blast radius, dependencies, and affected flows before coding.
3. **G4: Validate development.** Run 
pm run lint`, 
px tsc --noEmit`, unit tests, and 
pm run build`. Capture output and connect it to the acceptance criteria.
4. **G5: Run independent QA.** Run 
px playwright test` plus accessibility, responsive, browser, loading, error, empty, and critical-journey checks.
5. **G6: Review governance.** Show static analysis, dependency and secret scans, authorization checks, Server/Client boundary checks, and regression findings.
6. **G7: Package release evidence.** Show screenshots or logs, end-to-end results, rollback or migration notes, and the RFQA package.
7. **G8: Close the learning loop.** Record defects, root causes, lessons learned, new tests, reusable patterns, and guideline updates.

### Evidence record

For each gate, record: `stage`, `owner`, `status`, `prerequisites`, `deliverables`, `commands`, `results`, `acceptanceCriteria`, `affectedFlows`, `evidence`, `risks`, `blockers`, and 
extAction`. Store durable project evidence under `docs/`, including `docs/traceability-matrix.md`, and test code under `tests/` in the generated application.

A gate is `BLOCKED` when evidence is missing, a check is unavailable, or a critical test fails. The demo should show the blocker and smallest corrective action rather than presenting an unverified pass.

## Key guardrails to mention

- No code is generated before discovery is complete.
- New applications must be created outside this agent repository in a fresh,
  empty folder.
- Server Components are the default; client components are used only when
  browser interactivity needs them.
- Secrets, authentication, Prisma, and private environment variables stay on
  the server.
- Generated apps cannot remain a bare Next.js starter page: they require real
  navigation, search/filter/sort behavior, responsive layouts, and user-facing
  loading, empty, and error states.