# Repository Instructions (always applied, all agents/modes)

This repository defines specialized custom agents under `.github/agents/` and
`.github/skills/**/agents/`. These rule files ONLY take effect when their
custom agent is explicitly selected in the agent picker (or delegated to as a
subagent). In default chat (no custom agent selected), none of those files are
loaded automatically — this file is the only instruction guaranteed to apply
everywhere, so it enforces the same non-negotiable rules directly.

## Next.js Request Classification

When the Next.js agent is active, classify each request as exactly one of: User
Prompt, Figma Link / MCP Design, Design Screenshot / UI Image, Jira User Story,
Azure DevOps Story, Bug Ticket, Enhancement Request, or Existing Application
Code. Existing-app changes, bug fixes, enhancements, and refactors bypass the
new-project discovery questionnaire. Before implementation, require concise
requirement, functional, non-functional, gap, impact, and technical analysis,
including goals, flows, edge cases, validation, dependencies, risks, affected
modules, assumptions, scope, and acceptance-criteria traceability. For bugs,
also require a falsifiable root-cause hypothesis, blast radius, and cheapest
disconfirming check. For design input, require a design inventory before code.

## HARD GATE — New Application / Project Requests

If the user asks to create, scaffold, or bootstrap a new application (Next.js,
React, mobile, or any other platform in this repo) and no matching custom agent
is currently active:

1. Do NOT run `create-next-app`, any other scaffolding CLI, or generate project
   files immediately.
2. Tell the user which custom agent should be used for this request (see
   Routing Table below) and ask them to select it from the agent picker, or —
   if the user explicitly wants you to proceed in the current chat — adopt that
   agent's full rule file as your instructions for the rest of the task before
   doing anything else.
3. Once acting under a platform agent's rules, run its Mandatory Discovery
   Protocol one question at a time and wait for each answer. Never skip
   discovery just because a project name or type was mentioned in passing.

## Routing Table

| Request mentions | Use this agent |
|---|---|
| Next.js, App Router, React Server Components | `.github/agents/NEXTJS_AGENT.agent.md` |
| React (non-Next.js) | `.github/agents/REACT_JS_AGENT.md` |
| Native Android | `.github/agents/ANDROID_NATIVE_AGENT.md` |
| Native iOS | `.github/agents/IOS_NATIVE_AGENT.md` |
| Flutter | `.github/agents/FLUTTER_HYBRID_AGENT.md` |
| React Native | `.github/agents/REACT_NATIVE_HYBRID_AGENT.md` |
| QA / test automation | `.github/agents/QA_AUTOMATION_AGENT.md` |
| Unclear platform | `.github/agents/ORCHESTRATOR_AGENT.md` (router) |

## Non-Negotiable Baseline for Any Generated Web/App UI

Regardless of which agent is active, a generated application is never allowed
to be a bare framework starter page. Every scaffolded app must include:

- A header with real category/section navigation (never a blank/default nav).
- A footer with its own footer-category navigation (Company, Support, Legal,
  Resources, etc.), not just copyright text.
- An aligned main content section (heading, description, responsive
  grid/list) — never the framework's default starter content.
- A search bar paired with at least one filter and one sort control, with
  state reflected in the URL.
- Shared UI organized with Atomic Design (`atoms/molecules/organisms/templates`
  under `components/`) per
  `.github/skills/web/nextjs/skills/architecture.skill.md` for Next.js work.

Removing the framework's default starter page/content is mandatory before a
scaffold task can be considered complete.

## Next.js Application-Type Routing

When a Next.js request is being handled, ask for the application type before
generation and offer exactly: Ecommerce, Healthcare, Technology / SaaS,
Portfolio, Blog, or Other. Store the answer as `APPLICATION_TYPE` and use it
to select the implementation template. Ecommerce must not be used as a
fallback for another choice.

The generated navigation and routes must match the choice: Ecommerce uses
products, product details, cart, checkout, orders, and admin products;
Healthcare uses patients, patient details, appointments, reports, and admin;
Technology / SaaS uses features, pricing, dashboard, projects, and settings;
Portfolio uses work, project details, about, and contact; Blog uses posts, post
details, categories, and about. For Other, ask for domain workflows and create
a custom route map. Every route must be implemented, linked, responsive, and
backed by typed dynamic data with relevant loading, empty, error, search,
filter, and sort states.

## Next.js Project Placement

Every new Next.js application must be created in a fresh, user-named folder
outside the `Orchestrator-Agent` repository. Ask for and verify the project
name and destination path before running any scaffold command. The destination
must be empty or newly created. Never scaffold with `create-next-app .` inside
the agent repository, reuse an existing app folder, or overwrite files.
