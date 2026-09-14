---
name: unit-testing.skill
description: Write unit, integration, accessibility, and cross-browser tests for React JS components and hooks using Jest, React Testing Library, jest-axe, MSW, and Playwright
---

# Skill: Unit Testing — React JS

This skill writes unit and integration tests for React JS using **Jest** and **React Testing Library (RTL)**, validates accessibility with **jest-axe**, mocks APIs with **MSW**, and covers browser journeys with **Playwright**.

Before choosing tools, inspect `package.json`, scripts, lockfile, and configuration. Reuse the existing test runner (Jest, Vitest, or another configured runner) instead of introducing a new one.

---

## Testing Stack

| Tool | Purpose |
|---|---|
| **Jest** | Test runner, mocking, assertions |
| **React Testing Library** | Component rendering and user interaction |
| **@testing-library/user-event** | Realistic user event simulation |
| **MSW (Mock Service Worker)** | API mocking for integration tests |
| **jest-dom** | DOM assertion matchers |
| **jest-axe** | Automated accessibility assertions (WCAG rule checks) |
| **Playwright** | Cross-browser and responsive end-to-end journeys |

---

## Setup

```ts
// jest.setup.ts
import '@testing-library/jest-dom';
```

```json
// vite.config.ts (test section)
{
  "test": {
    "globals": true,
    "environment": "jsdom",
    "setupFiles": ["./jest.setup.ts"]
  }
}
```

---

## Component Test Pattern

```tsx
// UserCard.test.tsx
import { render, screen } from '@testing-library/react';
import { UserCard } from './UserCard';

describe('UserCard', () => {
  it('renders the user name and email', () => {
    render(<UserCard name="Jane Doe" email="jane@example.com" />);

    expect(screen.getByText('Jane Doe')).toBeInTheDocument();
    expect(screen.getByText('jane@example.com')).toBeInTheDocument();
  });

  it('renders avatar when avatarUrl is provided', () => {
    render(<UserCard name="Jane" email="jane@example.com" avatarUrl="/img/jane.png" />);

    expect(screen.getByRole('img', { name: /jane/i })).toHaveAttribute('src', '/img/jane.png');
  });
});
```

---

## Hook Test Pattern

```ts
// useCounter.test.ts
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

describe('useCounter', () => {
  it('starts at the initial value', () => {
    const { result } = renderHook(() => useCounter(5));
    expect(result.current.count).toBe(5);
  });

  it('increments the count', () => {
    const { result } = renderHook(() => useCounter(0));
    act(() => result.current.increment());
    expect(result.current.count).toBe(1);
  });
});
```

---

## Form Interaction Test Pattern

```tsx
// LoginForm.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  it('calls onSubmit with email and password', async () => {
    const user = userEvent.setup();
    const onSubmit = jest.fn();

    render(<LoginForm onSubmit={onSubmit} />);

    await user.type(screen.getByLabelText(/email/i), 'user@example.com');
    await user.type(screen.getByLabelText(/password/i), 'secret123');
    await user.click(screen.getByRole('button', { name: /log in/i }));

    expect(onSubmit).toHaveBeenCalledWith({
      email: 'user@example.com',
      password: 'secret123',
    });
  });

  it('shows validation error when email is empty', async () => {
    const user = userEvent.setup();
    render(<LoginForm onSubmit={jest.fn()} />);

    await user.click(screen.getByRole('button', { name: /log in/i }));

    expect(screen.getByText(/email is required/i)).toBeInTheDocument();
  });
});
```

---

## API Mocking with MSW

```ts
// mocks/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('/api/users/:id', ({ params }) => {
    return HttpResponse.json({ id: params.id, name: 'Jane Doe' });
  }),
  http.post('/api/auth/login', async ({ request }) => {
    const body = await request.json();
    if (body.password !== 'secret123') {
      return HttpResponse.json({ message: 'Invalid credentials' }, { status: 401 });
    }
    return HttpResponse.json({ token: 'test-token' });
  }),
];
```

Use MSW handlers for every integration test that exercises a data-fetching hook, React Query/RTK Query endpoint, or component that calls the network. Never mock `fetch`/`axios` directly with `jest.spyOn` when MSW can intercept the request instead.

---

## Redux-Connected Component Test Pattern

```tsx
// Wrap with a test store provider
import { configureStore } from '@reduxjs/toolkit';
import { Provider } from 'react-redux';

function renderWithStore(ui: ReactElement, preloadedState = {}) {
  const store = configureStore({ reducer: rootReducer, preloadedState });
  return render(<Provider store={store}>{ui}</Provider>);
}
```

---

## Accessibility Test Pattern

Follow W3C WCAG 2.0, 2.1, and 2.2 by default, targeting **WCAG 2.2 Level AA**. Every component/page test suite includes an automated `jest-axe` check plus manual assertions for keyboard operability, labeling, and focus — map failures to the relevant success-criterion ID.

```tsx
// UserCard.a11y.test.tsx
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';
import { UserCard } from './UserCard';

expect.extend(toHaveNoViolations);

describe('UserCard accessibility', () => {
  it('has no detectable WCAG violations', async () => {
    const { container } = render(<UserCard name="Jane Doe" email="jane@example.com" />);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });
});
```

---

## Playwright Cross-Browser Testing

See `skills/playwright.skill.md` for the full test-organization, locator, and cross-browser/responsive matrix guidance. Use the existing Playwright configuration, fixtures, test directory, and base URL. Prefer role, label, placeholder, and stable test-id locators; assert visible outcomes, URLs, validation, focus, and accessible state instead of arbitrary sleeps.

```ts
// login.spec.ts
import { test, expect } from '@playwright/test';

test('user can log in with valid credentials', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel(/email/i).fill('user@example.com');
  await page.getByLabel(/password/i).fill('secret123');
  await page.getByRole('button', { name: /log in/i }).click();

  await expect(page).toHaveURL('/dashboard');
  await expect(page.getByRole('heading', { name: /dashboard/i })).toBeFocused();
});
```

---

## Test-Case Matrix

Before writing tests, map every acceptance criterion to a test case: ID, type (unit/integration/e2e/accessibility), flow, expected result, command, and evidence. Cover happy paths, boundaries, loading, empty, error, unauthorized, pending, success, security, and regression states. Keep tests deterministic, isolated, and boundary-mocked.

| Test ID | Type | Flow | Expected Result | Command |
|---|---|---|---|---|
| AC-1-01 | unit | Renders with valid props | Name and email visible | `npm test UserCard` |
| AC-1-02 | accessibility | jest-axe scan | No WCAG violations | `npm test UserCard.a11y` |
| AC-2-01 | e2e | Login happy path | Redirects to `/dashboard` | `npx playwright test login` |

Run focused tests, lint, typecheck, unit/integration tests, production build, and the Playwright suite in order. Do not waive critical failures without explicit approval.

---

## Rules

- Test **behavior**, not implementation — never test internal state directly.
- Query by **role** first (`getByRole`), then label, then text. Avoid `getByTestId`.
- Use `userEvent` over `fireEvent` for interaction tests.
- Mock API calls with **MSW**, not `jest.spyOn(fetch)`.
- One `describe` block per component or hook.
- Aim for **80%+ coverage** on business logic hooks and organisms.
- Never snapshot-test dynamic content.
- Every component/page suite includes a `jest-axe` accessibility check.
- Critical user journeys are covered by Playwright across Chromium, Firefox, WebKit, and applicable mobile/viewport combinations.

---

## Capabilities

- Generate unit tests for a given component file.
- Generate hook tests for a given custom hook.
- Generate integration tests for a form or page, mocked with MSW.
- Generate RTK Query / React Query endpoint mock tests.
- Generate accessibility (`jest-axe`) tests for a component.
- Generate Playwright cross-browser/responsive test specs.
- Generate a test-case matrix mapping acceptance criteria to tests.
- Generate a test setup file for a new project.
