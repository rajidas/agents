# Skill: Angular Playwright Testing

## Purpose

Create reliable browser tests for critical Angular user journeys.

## Rules

- Inspect supported browser policy and existing projects before changing
	configuration. Run supported Chromium, Firefox, and WebKit projects for
	critical journeys, adding mobile Chromium/WebKit when mobile web is in scope.
- Validate critical routes at desktop, tablet, and narrow-mobile viewports;
	check text fit, overflow, touch targets, layout shifts, and horizontal scroll.
- Record browser, viewport, command, result, and screenshot/trace evidence;
	report unsupported or skipped coverage with residual risk.

- Use the existing Playwright configuration, test directory, fixtures, and base URL.
- Start with a smoke test for `/` that checks a heading, primary landmark, and main navigation.
- Prefer role, label, placeholder, and stable test-id locators over generated CSS selectors.
- Assert visible outcomes, URLs, validation, focus, and accessible state; avoid arbitrary sleeps.
- Isolate test data and authentication state with fixtures or API setup.
- Cover loading, empty, invalid-input, error, unauthorized, pending, success, keyboard, and responsive states where relevant.
- Mock external services at stable boundaries while retaining an integration path for critical configuration.

## Angular Considerations

Wait for meaningful rendered states rather than generic network-idle conditions. Test lazy routes, guards, dialogs, form validation, and error handlers when they are part of the workflow.

## Completion

Run focused specs, the smoke suite, and the full configured browser suite before release. Report browser projects, commands, failures, and residual risk.
