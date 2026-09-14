---
name: playwright.skill
description: Generate reliable Playwright tests for critical React SPA user journeys, including cross-browser and responsive coverage.
---

# Skill: Playwright Generator

## Purpose

Generate reliable Playwright tests for critical React user journeys. Test the public behavior a user depends on, including navigation, forms, authentication, loading states, errors, and successful mutations.

## Test Organization

Use the existing test directory and configuration. If no convention exists,
prefer:

```text
playwright.config.ts
tests/
	smoke.spec.ts
	auth.spec.ts
	dashboard.spec.ts
	fixtures/
		auth.fixture.ts
		test-data.ts
```

Keep each spec focused on one workflow. Reuse fixtures for setup, but do not
hide the behavior being asserted behind large helper abstractions.

## Locator and Assertion Rules

- Prefer `getByRole` with an accessible name, then `getByLabel`,
	`getByPlaceholder`, or a stable `data-testid` when no semantic locator exists.
- Do not use brittle CSS selectors, generated class names, or text fragments
	that are likely to change with copy edits.
- Assert visible outcomes and URL/route changes, not internal React state or DOM
	implementation details.
- Use web-first assertions such as `toBeVisible`, `toHaveText`,
	`toHaveURL`, and `toBeEnabled`; avoid arbitrary sleeps.
- Verify the primary landmark and page heading so route and accessibility
	regressions fail early.

## Cross-Browser and Responsive Matrix

Use the project's supported-browser policy and existing Playwright projects as
the source of truth. For critical journeys, run the configured desktop
Chromium, Firefox, and WebKit projects when supported, and add mobile Chromium
and mobile WebKit when mobile web is in scope. Do not silently omit an
unsupported project; record the reason and residual risk.

Validate every critical route at supported desktop, tablet, and narrow-mobile
viewports. Check responsive navigation, forms, dialogs, tables, loading and
error states, keyboard focus, text wrapping, overflow, touch targets, layout
shifts, and horizontal scrolling. Record browser, viewport, command, outcome,
and screenshot/trace evidence in the QA report.

## Required Coverage

Start every project with a smoke test for `/` that verifies:

- The page loads without a console or uncaught runtime error.
- The expected page heading and primary landmark are visible.
- The main navigation can reach the primary workflow.

For each critical workflow, cover the appropriate states:

- Initial loading or pending state.
- Empty state when no records exist.
- Invalid input and visible validation feedback.
- Unauthenticated and forbidden access behavior (protected routes redirect as expected).
- Server or network failure behavior.
- Successful creation, update, deletion, or navigation.
- Keyboard access and focus behavior for interactive controls.

## Fixtures and Test Data

- Use isolated test data with unique identifiers and deterministic values.
- Prefer API setup, mocked responses (MSW at the network layer, or Playwright
	route interception), or seeded test data over clicking through unrelated
	screens to prepare every test.
- Clean up created records or use an isolated backend/mock per test run.
- Keep authentication state in a dedicated fixture and never commit credentials.
- Use environment variables for test-only URLs and credentials.

## React SPA Runtime Considerations

- Configure `baseURL` and use relative paths so local and CI runs share tests.
- Wait for meaningful UI states instead of arbitrary network-idle conditions;
	client-side data fetching and suspense boundaries may keep connections open.
- Test React Router redirects, protected routes, dynamic route params, and
	error boundaries when they are part of the user journey.
- Mock third-party services at a stable boundary (e.g. intercept network
	requests or reuse MSW handlers), but keep one integration path for critical
	configuration and authentication behavior.
- Use request interception only when the test is specifically about a client
	failure state; do not mock the application under test by default.

## Generation Steps

1. Identify the critical journey and its acceptance criteria.
2. Add or reuse a fixture for authentication and deterministic test data.
3. Write the smallest smoke test, using semantic locators.
4. Add success and failure cases for the workflow's highest-risk behavior.
5. Run the focused spec in headed or trace mode while developing.
6. Run the complete smoke suite and then the full Playwright suite before
	 release.

## Completion Checklist

- Tests use stable, accessible locators.
- Assertions describe user-visible behavior.
- Tests do not depend on arbitrary timeouts or execution order.
- Test data and authentication are isolated.
- Loading, empty, error, auth, and success states are covered where relevant.
- The smoke suite passes in the configured CI browser project.
