# React Workspace Instructions

## Required Flow

For every React request, classify the input as User Prompt, Figma/MCP Design,
Screenshot/Image, Jira Story, Azure DevOps Story, Bug Ticket, Enhancement, or
Existing Application Code. Route through source-specific analysis, UI layout,
functional development, accessibility validation, test-case generation,
automated unit/integration testing, and cross-browser/responsive testing.
Existing-app changes bypass new-project discovery. Use WCAG 2.0, 2.1, and 2.2
by default with WCAG 2.2 Level AA as the primary target, and map acceptance
criteria to implementation and test evidence.

- Use the React orchestrator and worker agents under `agents/` for multi-step React work.
- Apply `skills/architecture.skill.md` to structure, `skills/ui_standards.skill.md` to UI, `skills/unit-testing.skill.md` to unit/integration/accessibility tests, and `skills/playwright.skill.md` to browser validation.
- Preserve function components, hooks, strict TypeScript, React Router, and the project's established UI library and state strategy (Redux Toolkit, React Query/TanStack Query, Zustand, or Context).
- Inspect before editing, keep components thin, and run focused validation before broad checks.
- Never scaffold a new React app inside the `Orchestrator-Agent` repository or overwrite an existing project.
