# Angular Workspace Instructions

## Required Flow

For every Angular request, classify the input as User Prompt, Figma/MCP Design,
Screenshot/Image, Jira Story, Azure DevOps Story, Bug Ticket, Enhancement, or
Existing Application Code. Route through source-specific analysis, UI layout,
functional development, accessibility validation, test-case generation,
automated unit/integration testing, and cross-browser/responsive testing.
Existing-app changes bypass new-project discovery. Use WCAG 2.0, 2.1, and 2.2
by default with WCAG 2.2 Level AA as the primary target, and map acceptance
criteria to implementation and test evidence.

- Use the Angular orchestrator and worker agents under `agents/` for multi-step Angular work.
- Apply `skills/architecture.skill.md` to structure, `skills/ui_standards.skill.md` to UI, `skills/http.skill.md` to API calls, `skills/state.skill.md` to state, `skills/data.skill.md` to contracts, and `skills/playwright.skill.md` to browser validation.
- Preserve standalone components, strict TypeScript, Angular Router, dependency injection, Signals, RxJS, and the project's established UI library.
- Inspect before editing, keep components thin, and run focused validation before broad checks.
- For attached screenshot UI requests, inspect the screenshot before any UI implementation, record its visual and viewport analysis, implement desktop/tablet and mobile from their respective evidence, and compare rendered output against each supplied image before QA.
- If a required spec is missing, create it using the project's existing test runner and conventions, then run the focused test, complete test suite, lint, typecheck, build, accessibility checks, and configured browser tests.
- Never scaffold a new Angular app inside the `Orchestrator-Agent` repository or overwrite an existing project.
