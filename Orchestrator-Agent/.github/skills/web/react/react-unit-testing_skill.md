---
name: react-unit-testing_skill
description: Write unit and integration tests for React JS components and hooks using Jest and React Testing Library
---

# Unit Testing Skill — React JS

This skill writes unit and integration tests for React JS using **Jest** and **React Testing Library (RTL)**.

---

## Testing Stack

| Tool | Purpose |
|---|---|
| **Jest** | Test runner, mocking, assertions |
| **React Testing Library** | Component rendering and user interaction |
| **@testing-library/user-event** | Realistic user event simulation |
| **MSW (Mock Service Worker)** | API mocking for integration tests |
| **jest-dom** | DOM assertion matchers |

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

## Rules

- Test **behavior**, not implementation — never test internal state directly.
- Query by **role** first (`getByRole`), then label, then text. Avoid `getByTestId`.
- Use `userEvent` over `fireEvent` for interaction tests.
- Mock API calls with **MSW**, not `jest.spyOn(fetch)`.
- One `describe` block per component or hook.
- Aim for **80%+ coverage** on business logic hooks and organisms.
- Never snapshot-test dynamic content.

---

## Capabilities

- Generate unit tests for a given component file
- Generate hook tests for a given custom hook
- Generate integration tests for a form or page
- Generate RTK Query endpoint mock tests
- Generate a test setup file for a new project

---

*This is a skill used by the @REACT_JS_AGENT agent.*
