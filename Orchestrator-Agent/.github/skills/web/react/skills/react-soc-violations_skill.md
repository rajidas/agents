---
name: react-soc-violations_skill
description: Review a React JS codebase for Separation of Concerns violations and suggest refactoring
---

# SoC Violations Review Skill

This skill audits a React JS project for **Separation of Concerns (SoC) violations** and produces an actionable refactoring report.

---

## What Is a SoC Violation in React?

A SoC violation occurs when a component or module handles responsibilities that belong to a different layer.

---

## Common Violation Types

### 1. Business Logic in JSX

❌ **Violation**
```tsx
function OrderList() {
  const [orders, setOrders] = useState([]);

  useEffect(() => {
    fetch('/api/orders')
      .then((res) => res.json())
      .then((data) => setOrders(data.filter((o) => o.status === 'active')));
  }, []);

  const total = orders.reduce((sum, o) => sum + o.amount, 0);

  return <div>Total: {total}</div>;
}
```

✅ **Fix** — Extract to a hook and a selector:
```tsx
// useActiveOrders.ts
export function useActiveOrders() {
  const { data: orders = [] } = useGetOrdersQuery();
  const activeOrders = orders.filter((o) => o.status === 'active');
  const total = activeOrders.reduce((sum, o) => sum + o.amount, 0);
  return { activeOrders, total };
}

// OrderList.tsx
export function OrderList() {
  const { activeOrders, total } = useActiveOrders();
  return <div>Total: {total}</div>;
}
```

---

### 2. API Calls Inside Components

❌ **Violation**: `fetch` or `axios` called directly inside a component body or `useEffect`.

✅ **Fix**: Use RTK Query or React Query. All API calls live in API slice files.

---

### 3. Atoms / Molecules Containing State Logic

❌ **Violation**: A `Button` atom managing form submission state.

✅ **Fix**: Atoms are stateless. Move state to a parent molecule or organism.

---

### 4. Cross-Feature Direct Imports

❌ **Violation**:
```ts
import { UserProfile } from '../auth/components/UserProfile';
```

✅ **Fix**: Import only from the feature's public `index.ts`:
```ts
import { UserProfile } from '@/features/auth';
```

---

### 5. Prop Drilling (3+ Levels)

❌ **Violation**: Passing `user` prop through 3+ component layers.

✅ **Fix**: Use `useAppSelector` or React Context at the closest common ancestor.

---

### 6. Page Components With Business Logic

❌ **Violation**: A Page component directly calling API functions and transforming data.

✅ **Fix**: Pages only compose organisms. All logic lives in hooks or the Redux layer.

---

## Audit Process

When this skill is invoked:

1. Ask for the component or folder to review (or scan the full `src/` if not specified).
2. Scan for each violation type listed above.
3. Produce a report:

| File | Line | Violation Type | Severity | Suggested Fix |
|---|---|---|---|---|
| `OrderList.tsx` | 12 | Business logic in JSX | High | Extract to `useActiveOrders` hook |
| `Button.tsx` | 8 | State in atom | Medium | Move state to parent |

4. Provide refactored code for each High severity violation.

---

## Capabilities

- Full project SoC audit
- Single file or component review
- Refactoring suggestions with before/after code
- Priority ranking by severity (High / Medium / Low)

---

*This is a skill used by the @REACT_JS_AGENT agent.*
