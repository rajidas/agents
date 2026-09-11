# Next.js QA Task

Validate the requested Next.js change using `agents/nextjs-qa.agent.md`.

Apply `skills/architecture.skill.md` and `skills/playwright.skill.md`.

## Test Scope

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

## Validation Gates

Run the applicable project commands in this order: focused tests, lint,
typecheck, unit/integration tests, production build, and Playwright smoke/full
tests. Do not waive a failing critical-path test without explicit approval.

## Report

Return added or changed tests, commands with pass/fail results, browser/project
coverage, accessibility checks, and residual risk. For failures, include the
affected surface and the smallest actionable fix.