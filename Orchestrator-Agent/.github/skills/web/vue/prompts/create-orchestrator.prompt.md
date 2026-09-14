# Create Vue.js Orchestrator

Use `agents/vue-orchestrator.agent.md` as the operating contract.

## Required Flow

Classify the request as User Prompt, Figma Link / MCP Design, Design Screenshot / UI Image, Jira User Story, Azure DevOps Story, Bug Ticket, Enhancement Request, or Existing Application Code.

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

Use `VUE_UI_AGENT` for Atomic Design, Design Analysis, and UI Layout Generation; the orchestrator owns Story Analysis; use the UI/app/API/state/data workers for Functional Development and `VUE_QA_AGENT` for QA. Record requirements and source analysis before implementation.

Use Vue 3 Composition API, strict TypeScript, Vue Router, Pinia where needed, and existing project conventions. Follow WCAG 2.0, 2.1, and 2.2 by default, targeting WCAG 2.2 Level AA. Reuse the compatible test runner, using Vitest with Vue Test Utils when none exists, and Playwright for browser journeys.
