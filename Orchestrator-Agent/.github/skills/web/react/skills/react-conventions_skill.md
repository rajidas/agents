---
name: react-conventions_skill
description: React JS coding conventions and best practices for TypeScript, hooks, components, and file organization
---

# React Conventions Skill

This skill defines and enforces the coding conventions for React JS projects.

---

## Naming Conventions

| Item | Convention | Example |
|---|---|---|
| Components | PascalCase | `UserCard`, `LoginForm` |
| Hooks | camelCase prefixed with `use` | `useAuth`, `useFetchUsers` |
| Context | PascalCase + `Context` suffix | `ThemeContext`, `AuthContext` |
| Files (components) | PascalCase | `UserCard.tsx` |
| Files (hooks) | camelCase | `useAuth.ts` |
| Files (utils) | camelCase | `formatDate.ts` |
| Files (types) | PascalCase | `User.types.ts` |
| CSS Modules | camelCase | `styles.cardWrapper` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| Enums | PascalCase | `UserRole.Admin` |

---

## Component Conventions

```tsx
// ✅ Correct — named export, props interface, no default export for components
interface UserCardProps {
  name: string;
  email: string;
  avatarUrl?: string;
}

export function UserCard({ name, email, avatarUrl }: UserCardProps) {
  return (
    <div className={styles.card}>
      <span>{name}</span>
      <span>{email}</span>
    </div>
  );
}
```

- Always use **named exports** for components (never default export except for pages/routes).
- Define props as an `interface`, not `type`, unless union types are needed.
- Destructure props directly in the function signature.
- Keep components under **150 lines** — extract logic into hooks.

---

## Hook Conventions

```ts
// ✅ Correct — single responsibility, returns named values
export function useUserProfile(userId: string) {
  const [profile, setProfile] = useState<UserProfile | null>(null);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    // fetch logic
  }, [userId]);

  return { profile, isLoading, error };
}
```

- Hooks must start with `use`.
- One responsibility per hook.
- Return an **object** (not an array) unless mimicking `useState` behavior.
- Never call hooks conditionally.

---

## Import Order

Enforce with ESLint `import/order`:

1. React and React ecosystem (`react`, `react-dom`, `react-router-dom`)
2. Third-party libraries (`axios`, `lodash`, `date-fns`)
3. Internal aliases (`@/components`, `@/hooks`, `@/features`)
4. Relative imports (`./UserCard`, `../types`)
5. Type-only imports (`import type { User } from '@/types'`)
6. CSS / styles (last)

---

## TypeScript Conventions

- Never use `any` — use `unknown` + type narrowing instead.
- Prefer `interface` over `type` for object shapes.
- Use `type` for unions, intersections, and mapped types.
- All API response shapes must be typed.
- Use `satisfies` operator for config objects.

---

## File Conventions

- One component per file.
- Co-locate tests: `UserCard.test.tsx` next to `UserCard.tsx`.
- Co-locate stories: `UserCard.stories.tsx` next to `UserCard.tsx`.
- `index.ts` barrel files only at the feature boundary — not inside atomic folders.

---

## Forbidden Patterns

- ❌ Inline styles (use CSS Modules or Tailwind classes)
- ❌ `any` type
- ❌ `default export` for components (pages are the exception)
- ❌ Business logic inside JSX
- ❌ Direct DOM manipulation (`document.getElementById`)
- ❌ Prop drilling more than 2 levels deep (use Context or Redux)
- ❌ `useEffect` for data fetching (use React Query or RTK Query)

---

*This is a skill used by the @REACT_JS_AGENT agent.*
