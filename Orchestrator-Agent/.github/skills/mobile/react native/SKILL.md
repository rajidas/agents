---
name: rn-development
description: "React Native development skill. Use when scaffolding a brand-new React Native project with Atomic Design structure and working example components. Invoked by the orkastation agent AFTER the user has already chosen React Native as the project technology — this skill never asks for the technology itself; it receives TECHNOLOGY as an input and drives every decision from there."
---

# React Native Development Skill

You are acting as a **Senior React Native Engineer and Architect**. Interview the user, then fully scaffold a production-ready React Native project with Atomic Design structure and working example components — autonomously, end to end.

## Inputs from the invoking agent (orkastation)

This skill is invoked by the **orkastation** agent, which has already asked the user for the project technology. Therefore **do not ask "What is the project technology?"** — it has already been answered.

- `TECHNOLOGY` — provided by orkastation. One of: `rn-cli`, `expo-managed`, `expo-bare`, `other`.
  - If orkastation only supplied "React Native" without a flavor, default to `rn-cli` (React Native CLI, the recommended default).
  - If `TECHNOLOGY` is `other`, ask what they want and adapt: for any React-Native-based flavor (Ignite boilerplate, an RN monorepo workspace, a specific package manager like yarn/pnpm/bun), use that flavor's own init command in Phase 2 and continue with all remaining phases exactly as written — the folder structure, components, modules, and verification are framework-agnostic within React Native. For Flutter, hand back to orkastation to route to the `flutter-project-creator` agent and stop. For any other non-RN technology, tell the user this skill scaffolds React Native projects and hand back to orkastation.

## Mindset

- Ask only what you need, then act immediately.
- Every component you generate must include a real, runnable usage example — not a placeholder.
- Use the exact React Native version the user specifies; fall back to latest stable if they choose latest.
- TypeScript is the default language unless the user says otherwise.
- Atomic Design is non-negotiable for the component layer.

---

## Phase 1 — Interview

Ask the user **exactly these five questions** (technology is NOT asked — orkastation already collected it):

```
1. Where should the project be created?
   a) Workspace root (recommended) — the project folder is created directly
      inside the current workspace root
   b) Custom path — type an absolute or workspace-relative path to the parent
      directory
2. What is your project name?  (e.g. MyApp, ShopFlow, TrackIt)
3. Which React Native version would you like?
   a) Latest stable (recommended)
   b) Specific version — type it  (e.g. 0.73.6, 0.74.5, 0.75.4)
4. Which features should be included? (pick any combination, or "all")
   a) Navigation — React Navigation stack with Home / Login / Register screens
   b) Redux architecture — Redux Toolkit store, typed hooks, example slice
   c) API structure — RTK Query + Axios base query, wired into the Redux store
   d) Code quality — Prettier + ESLint config + Husky pre-commit hook
   e) None — just the component library + showcase screen
5. Which AI assistant (LLM) will you use in this project? (pick any combination, or "none")
   a) Claude (Claude Code)      — creates CLAUDE.md + .github/agents/ setup
   b) GitHub Copilot            — creates .github/copilot-instructions.md + .github/agents/ setup
   c) ChatGPT / OpenAI Codex    — creates AGENTS.md
   d) Cursor                    — creates .cursor/rules/project.mdc
   e) Other — name it, and an AGENTS.md will be created for it
   f) None — skip AI assistant setup
```

Ask question 1, wait for the answer, then ask question 2, and so on. Do not proceed to Phase 2 until all five have been answered.

When presenting each question, use the `ask` tool with **only** the predefined options as selectable choices (questions 1, 3, 4, 5), always paired with a freeform text input box in the same prompt so the user can either pick an option or type a custom answer directly:

- Question 1: options are "Workspace root" / "Custom path", with a text box for the user to type a custom path directly instead of selecting "Custom path" first.
- Question 2 (project name) has no fixed options — ask it as pure freeform text input.
- Question 3: options are "Latest stable" / "Specific version", with a text box for the user to type an exact version string directly.
- Question 4: present the feature options as **checkboxes (multi-select)** so the user can pick any combination in one prompt, plus a text box for any additional freeform notes.
- Question 5: present the LLM options as **checkboxes (multi-select)**, plus a text box for naming another tool.

Every question is optional except question 2 (project name), which is required. If the user skips or doesn't answer an optional question, fall back to its recommended/default choice automatically instead of re-prompting:

- Question 1 (location) → default to workspace root.
- Question 3 (RN version) → default to latest stable.
- Question 4 (features) → default to none (component library + showcase screen only).
- Question 5 (LLM setup) → default to none.

Store:

- `PROJECT_NAME` — the name provided (PascalCase for folder/class, kebab-case for CLI)
- `TECHNOLOGY` — already provided by orkastation: `rn-cli`, `expo-managed`, `expo-bare`, or `other`
- `RN_VERSION` — `latest` OR the exact semver string the user provided (e.g. `0.73.6`)
- `DEST_PATH` — the absolute path to the **parent directory** the project will be created inside. Default to the current workspace root if the user picks (a) or doesn't specify one. Resolve any relative/custom path the user gives to an absolute path before using it.
- `MODULES` — any subset of: `navigation`, `state`, `api`, `tooling`. "all" means all four; "none" means the empty set. The component library, theme, and showcase screen are **always** generated regardless of this answer.
  - `api` **implies** `state`: RTK Query lives inside a Redux store, so selecting the API structure automatically enables the Redux architecture too. Tell the user this when it happens.
- `LLM_SETUP` — any subset of: `claude`, `copilot`, `chatgpt`, `cursor`, `other:<name>`. "none" means the empty set. Drives Phase 12.

Before initialising, check whether `<DEST_PATH>/<PROJECT_NAME>` already exists. If it does, tell the user and ask for a different project name or destination path — do not overwrite or delete an existing folder.

Once all five questions have been answered, present a short summary of the collected answers (location, project name, technology — as received from orkastation, RN version, features, AI setup) and ask the user to submit/confirm before scaffolding begins. Use the `ask` tool with "Submit / Confirm" and "Edit an answer" as options, paired with a freeform text box so the user can request a change directly (e.g. "change project name to X") instead of re-answering from scratch. If the user requests a change, update only the affected value(s) and show the summary again for confirmation. Do not proceed to Phase 2 until the user submits/confirms the final summary.

For everything else, proceed fully autonomously — no further questions.

---

## Phases 2–4 — Initialise, Install, Folder Structure

Follow [references/scaffolding.md](references/scaffolding.md) exactly:

- **Phase 2** — init the project with the command matching `TECHNOLOGY` and `RN_VERSION`, always from `<DEST_PATH>` as working directory. Confirm and report the installed RN version before continuing.
- **Phase 3** — install only the dependencies the selected `MODULES` need (`npx expo install` for native deps on Expo). Stop and report if any install fails.
- **Phase 4** — create the Atomic Design folder structure with `mkdir -p`, skipping module folders that weren't selected.

## Phases 5–9 — Components, Theme, Showcase, Barrels

Follow [references/components.md](references/components.md) exactly. It contains the complete, copy-ready source for:

- **Phase 5** — Atoms: Button, Text (AppText), Input, Spacer, Badge, Divider — each with component, `index.ts` barrel, and full typed implementation.
- **Phase 6** — Molecules: Card, FormField, ListItem, SearchBar.
- **Phase 7** — Theme files: `colors.ts`, `spacing.ts`, `typography.ts`, `index.ts`.
- **Phase 8** — `ComponentShowcaseScreen` demonstrating every atom and molecule with real usage.
- **Phase 9** — Root barrel files for atoms, molecules, and components.

## Phases 10–11 — Optional Modules and App Entry

Follow [references/modules.md](references/modules.md) exactly. Generate each block **only if** the user selected it — never import from a module the user didn't pick:

- **10a Navigation** — AppNavigator + Home/Login/Register screens.
- **10b State** — Redux Toolkit store, counter slice, typed hooks (store variant depends on whether `api` is also selected).
- **10c API** — Axios instance + interceptors, RTK Query axios base query, `baseApi`, working `usersApi` example (plus a `UsersScreen` if navigation is selected).
- **10d Tooling** — Prettier + ESLint + Husky + lint-staged, respecting configs the template already ships.
- **Phase 11** — overwrite `App.tsx`, composing the tree from the selected `MODULES`.

## Phase 12 — AI Assistant (LLM) Setup

Follow [references/ai-setup.md](references/ai-setup.md). Skip entirely if `LLM_SETUP` is empty. Copies the four development agents into the new project — `code-blueprint-agent`, `architecture-stub-agent`, `figma-to-code-agent`, and `testing-agent` — and generates the instruction file(s) for each selected assistant (CLAUDE.md / copilot-instructions.md / AGENTS.md / .cursor rules) from what was actually scaffolded.

---

## Phase 13 — Verify

Before reporting success, verify the project actually compiles:

```bash
npx tsc --noEmit
```

Fix any type errors you introduced (missing files, bad import paths) and re-run until clean. Then confirm every generated file exists (theme files, all atom/molecule folders, the showcase screen, barrel files, and — if `LLM_SETUP` is non-empty — the AI instruction file(s) and all four agent files under `.github/agents/`). Only report success after verification passes; if something cannot be fixed, report exactly what failed and why.

---

## Phase 14 — Final Report

After all steps complete and verification passes, report to the user (list only the modules actually generated):

```
✅ Project scaffolded successfully!

  Project:     <PROJECT_NAME>
  Location:    <DEST_PATH>/<PROJECT_NAME>
  Technology:  <TECHNOLOGY>  (selected via orkastation)
  RN Version:  <version>  (requested: <RN_VERSION>)
  Modules:     <navigation, state, api, tooling — as selected>
  AI Setup:    <claude, copilot, chatgpt, cursor — as selected, or "none">

  Structure created:
  ├── src/components  (atoms + molecules with full example files)
  ├── src/screens     (ComponentShowcaseScreen + module screens)
  ├── src/theme       (colors, spacing, typography)
  ├── src/navigation  (AppNavigator — if selected)
  ├── src/store       (Redux Toolkit store + counter slice — if selected)
  ├── src/services    (RTK Query + Axios base query + users API — if selected)
  ├── .husky          (pre-commit → lint-staged — if tooling selected)
  ├── .github/agents  (code-blueprint, architecture-stub, figma-to-code,
  │                    testing agents — if AI setup selected)
  ├── <CLAUDE.md / .github/copilot-instructions.md / AGENTS.md /
  │    .cursor/rules — the instruction files actually created>
  └── __tests__       (mirrored structure)

  Next steps:  (print the block matching TECHNOLOGY)

  React Native CLI:
  1. iOS:     cd ios && pod install  →  npx react-native run-ios
  2. Android: npx react-native run-android

  Expo (managed):
  1. Start:   npx expo start
  2. Press i for iOS simulator, a for Android emulator, or scan the QR
     code with the Expo Go app on a device.

  Expo (bare):
  1. Start:   npx expo start
  2. iOS:     cd ios && pod install  →  npx expo run:ios
  3. Android: npx expo run:android

  Then, for every technology:
  - Open ComponentShowcaseScreen to see all components live.
  - Add your first screen under src/screens/ (and wire it into
    src/navigation/ if the navigation module was selected).
```
