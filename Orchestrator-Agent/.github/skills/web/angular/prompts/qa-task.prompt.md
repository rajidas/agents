# Angular QA Task

Validate the requested Angular change using `agents/angular-qa.agent.md` and `skills/playwright.skill.md`.

Inspect `package.json`, scripts, configuration, lockfile, and dependencies.
Reuse the existing compatible Jasmine/Karma, Jest, Vitest, or other runner;
when none exists, choose the Angular-compatible default and document it. Use
Playwright for browser journeys. Create a test-case matrix mapping every
acceptance criterion to a test ID, type, flow, expected result, command, and
evidence. Cover success, validation boundaries, loading, empty, error,
unauthorized, pending, security, and regression states.

Inspect the existing test runner, browser configuration, fixtures, scripts, and supported browsers. Cover the primary route and landmark, navigation, success behavior, and highest-risk loading, empty, error, invalid-input, unauthorized, pending, keyboard, and responsive states. Prefer role, label, text, and stable test-id locators; avoid arbitrary waits and generated selectors.

Run focused tests, lint, typecheck, unit/integration tests, production build, and the configured browser suite in order. Report commands, results, coverage, and residual risk.

Run supported Chromium, Firefox, and WebKit projects, plus mobile projects when
applicable, at desktop, tablet, and narrow-mobile viewports. Check responsive
navigation, forms, dialogs, tables, keyboard focus, text wrapping, overflow,
touch targets, and layout shifts. Apply WCAG 2.0, 2.1, and 2.2 with WCAG 2.2
Level AA as the primary target, and report version/criterion evidence plus any
browser, viewport, or accessibility gaps.
