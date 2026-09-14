# Next.js QA Task

Validate the requested Next.js change using `agents/nextjs-qa.agent.md`.

Apply `skills/architecture.skill.md` and `skills/playwright.skill.md`.

## Test Scope

- Inspect `package.json`, package-manager lockfile, existing test scripts,
	test configuration, and installed dependencies before selecting a runner.
- Reuse the existing compatible unit/integration runner. If none exists, use
	Jest with the appropriate Next.js, TypeScript, and Testing Library setup;
	select another runner only when project compatibility provides a clear reason.
- Use Playwright for browser-level and critical user-journey tests. Do not add
	multiple unit-test runners without documenting the compatibility decision.
- Create a test-case matrix that maps every acceptance criterion to a test ID,
	test type, affected flow, expected result, command, and evidence.
- Inspect the project's existing scripts, Playwright configuration, fixtures,
	test data, and supported browser projects before adding tests.
- Add or update a smoke test for `/` and its primary landmark when no equivalent
	coverage exists.
- Cover the changed workflow's success path and highest-risk loading, empty,
	error, invalid-input, unauthorized, and pending states as applicable.
- Prefer `getByRole` with accessible names, then `getByLabel` or stable test IDs;
	avoid generated class names and arbitrary sleeps.
- Assert visible outcomes, URLs, response contracts, and focus behavior rather
	than implementation details.
- Isolate test data and authentication state. Use fixtures or API/database setup
	rather than depending on test order or unrelated UI flows.
- Keep external-service mocks at stable boundaries and do not mock the feature
	under test by default.
- Include happy paths, edge and validation boundaries, loading/pending, empty,
	error, unauthorized, success, security, and regression cases where relevant.
- Keep tests deterministic and isolated; report coverage, flaky or skipped
	tests, unavailable evidence, and remaining gaps.

## Cross-Browser and Responsive Scope

- Inspect supported browsers and the existing Playwright projects before
	adding or changing browser configuration.
- Run critical journeys in configured Chromium, Firefox, and WebKit projects
	when supported; record unsupported browsers and residual risk.
- Add mobile Chromium and mobile WebKit projects when mobile web is in scope.
- Validate critical routes at supported desktop, tablet, and narrow-mobile
	viewports, including navigation, forms, dialogs, tables, loading/error states,
	keyboard focus, text fit, overflow, touch targets, and layout shifts.
- Record browser, viewport, command, result, and artifact evidence for each
	critical workflow. A browser or viewport gap must be reported, not hidden.

## Validation Gates

Run the applicable project commands in this order: focused tests, lint,
typecheck, unit/integration tests, production build, and Playwright smoke/full
tests. Do not waive a failing critical-path test without explicit approval.

## Report
### Figma visual gate

For Figma-led work, capture Playwright screenshots at every supplied Figma frame dimension and at tablet and narrow mobile widths. Compare bounds, alignment, typography, colors, spacing, assets/icons, responsive behavior, overflow, and interaction states. Maintain a severity-ordered mismatch log and fix the largest mismatch first.

Missing major regions or approximation of available Figma assets with emoji, placeholders, random remote images, CSS drawings, or invented icons fails the gate. If MCP or reference renders are unavailable, report the visual gate as blocked and document residual risk.



Return added or changed tests, commands with pass/fail results, browser/project
coverage, accessibility checks, and residual risk. For failures, include the
affected surface and the smallest actionable fix.