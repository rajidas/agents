# Vue.js Full-Stack Workflow

## Input Classification and Flow

Classify every request as User Prompt, Figma Link / MCP Design, Design Screenshot / UI Image, Jira User Story, Azure DevOps Story, Bug Ticket, Enhancement Request, or Existing Application Code. Existing-app changes, bugs, enhancements, and refactors bypass new-project discovery.

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

Map the first three stages to `VUE_UI_AGENT`, Story Analysis to the orchestrator, Functional Development to UI/app/API/state/data workers, and QA to `VUE_QA_AGENT`. Record analysis before UI generation. Use WCAG 2.0, 2.1, and 2.2 by default with WCAG 2.2 Level AA as the target.

## Stages

1. Planning and source analysis.
2. Data contracts and mock/API boundaries.
3. API, authentication, and validation.
4. Vue Router, services, and application logic.
5. Pinia/composable state.
6. UI layouts and components.
7. Accessibility and responsive validation.
8. Compatible unit/integration tests and Playwright browser testing.
9. Production build and delivery evidence.

Inspect and reuse the existing test runner; use Vitest with Vue Test Utils when none exists. Validate supported Chromium, Firefox, WebKit, and applicable mobile projects across desktop, tablet, and narrow-mobile viewports.
