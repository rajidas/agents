# Phase 12 — AI Assistant (LLM) Setup (driven by `LLM_SETUP`)

Skip this phase entirely if `LLM_SETUP` is empty ("none").

All paths below are relative to the generated project root `<DEST_PATH>/<PROJECT_NAME>`.

## 12a — Copy the development agents into the project

For **every** non-empty `LLM_SETUP` selection, give the new project its own agents folder with the four development agents:

```bash
mkdir -p <DEST_PATH>/<PROJECT_NAME>/.github/agents
cp <STARTER_KIT_ROOT>/.github/agents/testing-agent.agent.md           <DEST_PATH>/<PROJECT_NAME>/.github/agents/
cp <STARTER_KIT_ROOT>/.github/agents/code-blueprint-agent.agent.md    <DEST_PATH>/<PROJECT_NAME>/.github/agents/
cp <STARTER_KIT_ROOT>/.github/agents/architecture-stub-agent.agent.md <DEST_PATH>/<PROJECT_NAME>/.github/agents/
cp <STARTER_KIT_ROOT>/.github/agents/figma-to-code-agent.agent.md     <DEST_PATH>/<PROJECT_NAME>/.github/agents/
```

`<STARTER_KIT_ROOT>` is the workspace this skill is running from (the starter kit containing `.github/agents/`). If a file cannot be found (e.g. the skill was invoked outside the starter kit), recreate it from scratch in the new project with the same role:

- `testing-agent.agent.md` — dynamic Testing Agent: detects the project stack, interviews the user (what to test, test types, depth, coverage), sets up Jest + React Native Testing Library if missing, writes real behavior-driven tests, and only reports success after the full suite is green.
- `code-blueprint-agent.agent.md` — Code Blueprint Agent: analyses a feature requirement against the project structure and writes a reviewable implementation blueprint (file plan, component breakdown, state design, API contracts, navigation, data flow, test plan) to `docs/blueprints/` — plans only, no production code.
- `architecture-stub-agent.agent.md` — Architecture Stub Agent: generates the complete compiling file skeleton for a feature (screens, components, slices, service endpoints, navigation entries, test stubs) following the project structure, with typed interfaces and TODO bodies.
- `figma-to-code-agent.agent.md` — Figma to Code Agent: converts Figma designs into screens/components that reuse the project's atoms/molecules and theme tokens, creates missing components at the right atomic level, and wires navigation.

## 12b — Generate the instruction file(s) for the selected assistant(s)

Write the **project context file** each selected assistant reads automatically. Generate the content dynamically from what was actually scaffolded — project name, `TECHNOLOGY`, the installed RN version, and only the `MODULES` that were selected. Never document a module that wasn't generated.

The shared body (reuse it for every selected assistant, adjusting only the filename/format):

```markdown
# <PROJECT_NAME>

<One-line description: React Native app scaffolded with Atomic Design.>

## Tech Stack
- React Native <version> (<rn-cli | expo-managed | expo-bare>), TypeScript
- <Only list selected modules: React Navigation / Redux Toolkit + typed hooks / RTK Query + Axios / Prettier + ESLint + Husky>

## Project Structure
- `src/components/` — Atomic Design: atoms → molecules → organisms → templates. Each component: `Component.tsx` + `index.ts` barrel.
- `src/theme/` — colors, spacing, typography. **Always use theme tokens; never hardcode colors or spacing values.**
- `src/screens/` — one folder per screen with an `index.ts` barrel.
- <Only if selected:> `src/navigation/` — AppNavigator + `RootStackParamList`; `src/store/` — Redux Toolkit slices + `useAppSelector`/`useAppDispatch`; `src/services/` — RTK Query endpoints injected into `baseApi` over an Axios base query.
- `__tests__/` — mirrors `src/`.

## Conventions
- TypeScript everywhere; typed props interfaces for every component.
- New components follow the existing atom/molecule pattern (variant + size maps, theme tokens, barrel exports).
- <Only if state selected:> Never mock `useSelector` in tests — render with a real `Provider`.
- <Only if api selected:> New endpoints are injected into `baseApi` via `injectEndpoints`; never create a second `createApi`.

## Commands
- Run: <the run commands matching TECHNOLOGY>
- Test: `npm test`
- Type-check: `npx tsc --noEmit`
- <Only if tooling selected:> Lint/format: `npm run lint` / `npm run format`

## Agents
- `.github/agents/code-blueprint-agent.agent.md` — plan a feature: produces an implementation blueprint in `docs/blueprints/` before any code is written.
- `.github/agents/architecture-stub-agent.agent.md` — scaffold a feature: generates the compiling file skeleton (screens, components, slices, services, tests) per the project structure.
- `.github/agents/figma-to-code-agent.agent.md` — implement UI from Figma designs, reusing the component library and theme tokens.
- `.github/agents/testing-agent.agent.md` — dynamic Testing Agent: invoke it to generate/fix tests for any file, feature, or the whole project.

Recommended feature workflow: blueprint → stub → figma-to-code (if a design exists) → implement logic → testing.
```

Per selection, write that body to:

- **`claude`** → `CLAUDE.md` at the project root.
- **`copilot`** → `.github/copilot-instructions.md`.
- **`chatgpt`** or **`other:<name>`** → `AGENTS.md` at the project root (for `other`, mention the named tool in the first line).
- **`cursor`** → `.cursor/rules/project.mdc`, with this frontmatter prepended:

  ```
  ---
  description: <PROJECT_NAME> project conventions
  alwaysApply: true
  ---
  ```

If multiple assistants are selected, create every corresponding file — they all share the same body, so keep them identical apart from format-specific wrapping.
