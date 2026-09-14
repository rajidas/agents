# Scaffold Angular App

Scaffold a production-ready Angular standalone application using the Angular worker agents and skills in this package.

## Required Discovery HARD GATE

First parse the complete current prompt and conversation. Store every supplied
value for `PROJECT_ID`, `APPLICATION_TYPE`, `DATA_TYPE`, `CATEGORY`, `UI_STYLE`,
`TECH_STACK`, `AUTH_TYPE`, `STATE_MANAGEMENT`, applicable `MODULES`,
`PROJECT_NAME`, and `PROJECT_PATH`. Clear natural-language requirements count
as answers. Never repeat an answered question. Ask only the first genuinely
missing value, one per turn. If none are missing, skip the questionnaire and
proceed to destination validation and scaffolding.

Do not run `ng new`, generate files, or plan architecture until the applicable
values are known. Reuse a project name and path already provided after verifying
the destination is outside `Orchestrator-Agent` and empty or newly created. Ask
for them only when genuinely absent.

Treat clear requirements as supplied answers. "Angular ecommerce" and "Angular
healthcare management" supply both application type and category; the same
applies to any clearly named domain. Named data sources, UI libraries, state
libraries, authentication methods, and pages or features supply their
corresponding discovery values. Do not ask for industry/category or admin
modules when the prompt already names the domain, pages, or features. Ask only
for values that remain genuinely unknown.

If the prompt names the application domain, record it as `APPLICATION_TYPE` and
skip the application-type question. Recognized examples include Ecommerce,
Healthcare, Education, Finance, Logistics, Hospital, SaaS, Portfolio, and Blog.
Ask only when no application domain can be inferred.

## Standards

- Latest stable Angular by default, or the explicitly requested compatible
	version; use TypeScript strict mode, standalone components, Angular Router,
	RxJS, and Signals.
- Use Angular Material, PrimeNG, or Tailwind only according to the selected or existing stack.
- Include a responsive header, category navigation, mobile menu, main landmark, search/filter/sort controls, loading, empty, error, and footer states.
- Use feature-based folders with `core`, `shared`, `layouts`, `features`, `services`, `guards`, `models`, `interceptors`, and `state` boundaries.
- Provide typed services, route guards where required, accessible forms, keyboard support, and a test baseline.
- Use the selected application type to determine routes and content; do not fall back to Ecommerce or leave dead placeholder routes.
- Keep secrets out of browser source and keep server persistence behind an API boundary.

Before implementation, determine and record the latest stable Angular and CLI
versions unless the user supplied a version. Produce the enterprise plan
including architecture, folder structure, route tree, UI wireframe, component
tree, service/state design, API and data contracts, sample pages, dashboard
design, responsive layout plan, deployment, and validation commands.

## Mandatory Execution Contract

The plan is not the deliverable. After discovery and plan approval, continue
through the complete implementation flow in the same task:

1. Scaffold the new Angular workspace in the verified destination.
2. Install only the dependencies selected by the user, such as Angular Material.
3. Replace the default starter screen with the requested application. Use the
	named domain rather than assuming Ecommerce.
4. Implement the shell, navigation, routes, pages, components, services, state,
	mock/API data boundary, authentication placeholders or integration, and
	responsive accessible UI described by the collected requirements.
5. Implement working interactions for the named domain's features. Use a typed
	mock repository when no backend was selected; do not create fake buttons
	with no behavior.
6. Add loading, empty, error, validation, pending, unauthorized, and not-found
	states where applicable.
7. Run focused tests, lint, typecheck, and production build, then repair issues
	in the generated application before reporting completion.

Installing Angular or Angular Material alone is never a successful completion.
Do not stop after `ng new`, `npm install`, dependency setup, or a planning
response. Report the implemented routes and features and include validation
results. If a command fails, fix the generated project or clearly report the
blocking error instead of claiming the application is complete.
