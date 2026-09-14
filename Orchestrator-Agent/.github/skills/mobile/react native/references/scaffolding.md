# Phases 2–4 — Initialise the Project, Install Dependencies, Folder Structure

`TECHNOLOGY` is provided by the orkastation agent — never ask for it here.

## Phase 2 — Initialise the Project

All commands in this phase run with `<DEST_PATH>` as the working directory, so the project folder is created directly under it — never nested inside another generated project. Always `cd` into `<DEST_PATH>` first:

```bash
cd <DEST_PATH>
```

### React Native CLI (`rn-cli`)

> The default template is already TypeScript (since RN 0.71). Do **not** pass
> `--template react-native-template-typescript` — that template is archived and
> the command fails with modern RN versions.
> Always run non-interactively: `npx -y` skips the npx install prompt, and
> `--install-pods false --skip-git-init` prevent the CLI from waiting on
> interactive prompts (pods are installed explicitly later).

**If `RN_VERSION` is `latest`:**

```bash
npx -y @react-native-community/cli@latest init <PROJECT_NAME> \
  --pm npm --install-pods false --skip-git-init
```

**If `RN_VERSION` is a specific version (e.g. `0.73.6`):**

```bash
npx -y @react-native-community/cli@latest init <PROJECT_NAME> \
  --version <RN_VERSION> \
  --pm npm --install-pods false --skip-git-init
```

If a versioned init fails (older RN versions predate the community CLI init flow), fall back to:

```bash
npx -y react-native@<RN_VERSION> init <PROJECT_NAME> --skip-install=false
```

```bash
cd <DEST_PATH>/<PROJECT_NAME>
```

Init can take several minutes (it runs a full `npm install`). Do not treat slow output as a hang; wait for it to finish and check the exit code before moving on. If the command errors, show the user the actual error output — do not silently continue to later phases.

### Expo Managed (`expo-managed`)

**If `RN_VERSION` is `latest`:**

```bash
npx -y create-expo-app@latest <PROJECT_NAME> --template blank-typescript --yes
```

**If `RN_VERSION` is a specific version:**

Expo pins React Native per SDK, so do **not** force `react-native@<RN_VERSION>` into an Expo project — mismatched versions break the build. Instead tell the user which Expo SDK maps to their requested RN version and scaffold with that SDK template, e.g. RN 0.74 → SDK 51:

```bash
npx -y create-expo-app@latest <PROJECT_NAME> --template blank-typescript@sdk-51 --yes
```

If you don't know the SDK mapping for the requested version, ask the user to pick an SDK or accept latest.

```bash
cd <DEST_PATH>/<PROJECT_NAME>
```

### Expo Bare (`expo-bare`)

Same rules as Expo Managed, with the `bare-minimum` template:

```bash
npx -y create-expo-app@latest <PROJECT_NAME> --template bare-minimum --yes
```

```bash
cd <DEST_PATH>/<PROJECT_NAME>
```

After init, confirm the installed React Native version with:

```bash
node -e "console.log(require('./node_modules/react-native/package.json').version)"
```

Report the version to the user before continuing.

---

## Phase 3 — Install Core Dependencies

> Do **not** install `@types/react-native` — it is deprecated; React Native has
> shipped its own types since 0.71 and the stub package causes type conflicts.

Install only what the selected `MODULES` need. Always required (showcase uses it): `react-native-safe-area-context`.

### React Native CLI (`rn-cli`)

```bash
# Always
npm install react-native-safe-area-context react-native-size-matters

# Only if MODULES includes `navigation`
npm install @react-navigation/native @react-navigation/native-stack react-native-screens

# Only if MODULES includes `state` (or `api`, which implies it)
npm install @reduxjs/toolkit react-redux

# Only if MODULES includes `api` (RTK Query ships inside @reduxjs/toolkit)
npm install axios

# Only if MODULES includes `state` or `api` (token/cache storage)
npm install @react-native-async-storage/async-storage

# Only if MODULES includes `tooling` (dev dependencies)
npm install --save-dev prettier eslint husky lint-staged
```

Then install iOS pods (only if running on macOS; skip with a note otherwise):

```bash
cd ios && pod install && cd ..
```

### Expo (managed or bare)

Use `npx expo install` for anything with native code so Expo picks versions compatible with the SDK; plain `npm install` here causes runtime crashes. Apply the same `MODULES` conditions as above:

```bash
# Always
npx expo install react-native-safe-area-context
npm install react-native-size-matters

# Only if MODULES includes `navigation`
npx expo install react-native-screens
npm install @react-navigation/native @react-navigation/native-stack

# Only if MODULES includes `state` (or `api`, which implies it)
npm install @reduxjs/toolkit react-redux

# Only if MODULES includes `api`
npm install axios

# Only if MODULES includes `state` or `api`
npx expo install @react-native-async-storage/async-storage

# Only if MODULES includes `tooling` (dev dependencies)
npm install --save-dev prettier eslint husky lint-staged
```

> RN CLI templates already ship an `.eslintrc` and `.prettierrc` — if they exist, extend them in the tooling module instead of installing/duplicating configs blindly.

If any install fails, stop and report the error rather than continuing to scaffold files.

---

## Phase 4 — Create the Folder Structure

Create the directories below with `mkdir -p`. Skip `navigation/` unless `MODULES` includes `navigation`, skip `store/` unless it includes `state`, and skip `services/` unless it includes `api`. Skip `screens/Auth/` unless `navigation` is selected (Login/Register only exist as navigable screens).

```
src/
├── components/
│   ├── atoms/
│   │   ├── Button/
│   │   ├── Text/
│   │   ├── Input/
│   │   ├── Icon/
│   │   ├── Spacer/
│   │   ├── Badge/
│   │   └── Divider/
│   ├── molecules/
│   │   ├── FormField/
│   │   ├── SearchBar/
│   │   ├── ListItem/
│   │   └── Card/
│   ├── organisms/
│   │   ├── Header/
│   │   ├── Footer/
│   │   └── Form/
│   ├── templates/
│   │   ├── MainTemplate/
│   │   └── AuthTemplate/
│   └── index.ts
├── screens/
│   ├── Home/
│   ├── Auth/
│   │   ├── Login/
│   │   └── Register/
│   └── index.ts
├── navigation/
├── hooks/
├── services/
├── store/
│   └── slices/
├── utils/
├── constants/
├── types/
├── theme/
├── assets/
│   ├── images/
│   ├── icons/
│   └── fonts/
└── config/
```

```
__tests__/
├── components/
│   ├── atoms/
│   ├── molecules/
│   └── organisms/
├── screens/
├── hooks/
└── utils/
```
