# Phases 10–11 — Optional Modules (driven by `MODULES`) and App Entry Point

Generate each block below **only if** the user selected it. Every generated file must compile against what actually exists — never import from a module the user didn't pick.

## 10a — Navigation (`MODULES` includes `navigation`)

**`src/navigation/AppNavigator.tsx`**

```typescript
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import HomeScreen from '../screens/Home/HomeScreen';
import LoginScreen from '../screens/Auth/Login/LoginScreen';
import RegisterScreen from '../screens/Auth/Register/RegisterScreen';
import ComponentShowcaseScreen from '../screens/ComponentShowcase/ComponentShowcaseScreen';

export type RootStackParamList = {
  Home: undefined;
  Login: undefined;
  Register: undefined;
  Showcase: undefined;
};

const Stack = createNativeStackNavigator<RootStackParamList>();

const AppNavigator: React.FC = () => (
  <NavigationContainer>
    <Stack.Navigator initialRouteName="Home">
      <Stack.Screen name="Home" component={HomeScreen} />
      <Stack.Screen name="Login" component={LoginScreen} />
      <Stack.Screen name="Register" component={RegisterScreen} />
      <Stack.Screen
        name="Showcase"
        component={ComponentShowcaseScreen}
        options={{ title: 'Components' }}
      />
    </Stack.Navigator>
  </NavigationContainer>
);

export default AppNavigator;
```

Also generate three small screens that use the atom/molecule library:

- **`src/screens/Home/HomeScreen.tsx`** — a `Card` with a welcome `AppText`, plus `Button`s navigating to `Showcase` and `Login` via `useNavigation<NativeStackNavigationProp<RootStackParamList>>()`.
- **`src/screens/Auth/Login/LoginScreen.tsx`** — email + password `FormField`s with the same inline validation pattern as the showcase, a submit `Button` (logs the values), and a `Button variant="ghost"` navigating to `Register`.
- **`src/screens/Auth/Register/RegisterScreen.tsx`** — name, email, password `FormField`s and a submit `Button`.

Each screen gets an `index.ts` barrel like the components do.

## 10b — State (`MODULES` includes `state`)

**`src/store/slices/counterSlice.ts`**

```typescript
import { createSlice, PayloadAction } from "@reduxjs/toolkit";

interface CounterState {
  value: number;
}

const initialState: CounterState = { value: 0 };

const counterSlice = createSlice({
  name: "counter",
  initialState,
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    setValue: (state, action: PayloadAction<number>) => {
      state.value = action.payload;
    },
  },
});

export const { increment, decrement, setValue } = counterSlice.actions;
export default counterSlice.reducer;
```

**`src/store/index.ts`** — without the `api` module:

```typescript
import { configureStore } from "@reduxjs/toolkit";
import { useDispatch, useSelector, TypedUseSelectorHook } from "react-redux";
import counterReducer from "./slices/counterSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

**`src/store/index.ts`** — with the `api` module (adds the RTK Query reducer + middleware):

```typescript
import { configureStore } from "@reduxjs/toolkit";
import { setupListeners } from "@reduxjs/toolkit/query";
import { useDispatch, useSelector, TypedUseSelectorHook } from "react-redux";
import counterReducer from "./slices/counterSlice";
import { baseApi } from "../services/baseApi";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    [baseApi.reducerPath]: baseApi.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(baseApi.middleware),
});

setupListeners(store.dispatch);

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
```

If `navigation` is also selected, add a working counter demo (value + increment/decrement `Button`s using `useAppSelector`/`useAppDispatch`) to `HomeScreen` so the store is visibly exercised.

## 10c — API structure: RTK Query + Axios (`MODULES` includes `api`)

Three layers: an Axios instance with interceptors, an RTK Query-compatible Axios base query, and one `baseApi` that feature endpoints inject into.

**`src/config/env.ts`**

```typescript
export const API_BASE_URL = "https://jsonplaceholder.typicode.com";
```

**`src/services/axiosInstance.ts`**

```typescript
import axios from "axios";
import AsyncStorage from "@react-native-async-storage/async-storage";
import { API_BASE_URL } from "../config/env";

export const axiosInstance = axios.create({
  baseURL: API_BASE_URL,
  timeout: 15000,
  headers: { "Content-Type": "application/json" },
});

axiosInstance.interceptors.request.use(async (config) => {
  const token = await AsyncStorage.getItem("auth_token");
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

axiosInstance.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Central place to handle expired sessions (e.g. clear token, redirect).
    }
    return Promise.reject(error);
  },
);
```

**`src/services/axiosBaseQuery.ts`**

```typescript
import type { BaseQueryFn } from "@reduxjs/toolkit/query";
import type { AxiosError, AxiosRequestConfig } from "axios";
import { axiosInstance } from "./axiosInstance";

export interface AxiosBaseQueryArgs {
  url: string;
  method?: AxiosRequestConfig["method"];
  data?: AxiosRequestConfig["data"];
  params?: AxiosRequestConfig["params"];
  headers?: AxiosRequestConfig["headers"];
}

export interface AxiosBaseQueryError {
  status?: number;
  data: unknown;
}

export const axiosBaseQuery =
  (): BaseQueryFn<AxiosBaseQueryArgs, unknown, AxiosBaseQueryError> =>
  async ({ url, method = "GET", data, params, headers }) => {
    try {
      const result = await axiosInstance.request({
        url,
        method,
        data,
        params,
        headers,
      });
      return { data: result.data };
    } catch (err) {
      const error = err as AxiosError;
      return {
        error: {
          status: error.response?.status,
          data: error.response?.data ?? error.message,
        },
      };
    }
  };
```

**`src/services/baseApi.ts`**

```typescript
import { createApi } from "@reduxjs/toolkit/query/react";
import { axiosBaseQuery } from "./axiosBaseQuery";

export const baseApi = createApi({
  reducerPath: "api",
  baseQuery: axiosBaseQuery(),
  tagTypes: ["Users", "Posts"],
  endpoints: () => ({}),
});
```

**`src/services/usersApi.ts`** — a working example endpoint (jsonplaceholder responds for real, so the demo actually loads data):

```typescript
import { baseApi } from "./baseApi";

export interface User {
  id: number;
  name: string;
  email: string;
  phone: string;
}

export const usersApi = baseApi.injectEndpoints({
  endpoints: (builder) => ({
    getUsers: builder.query<User[], void>({
      query: () => ({ url: "/users" }),
      providesTags: ["Users"],
    }),
    getUserById: builder.query<User, number>({
      query: (id) => ({ url: `/users/${id}` }),
      providesTags: (result, error, id) => [{ type: "Users", id }],
    }),
  }),
});

export const { useGetUsersQuery, useGetUserByIdQuery } = usersApi;
```

**`src/services/index.ts`**

```typescript
export { baseApi } from "./baseApi";
export { axiosInstance } from "./axiosInstance";
export * from "./usersApi";
```

If `navigation` is also selected, create `src/screens/Users/UsersScreen.tsx`, add it to the navigator as a `Users` route, and link to it from `HomeScreen`. It uses `useGetUsersQuery()` and renders: a loading `ActivityIndicator` while `isLoading`, an error `AppText variant="error"` with a retry `Button` calling `refetch` on `isError`, and a `FlatList` of `ListItem`s (name + email) on success — a complete, real RTK Query consumption example.

## 10d — Code quality: Prettier + ESLint + Husky (`MODULES` includes `tooling`)

RN CLI templates already ship `.prettierrc.js` and `.eslintrc.js` — keep them and only add what's missing. For Expo templates create the configs.

**`.prettierrc.js`** (create only if none exists):

```javascript
module.exports = {
  arrowParens: "avoid",
  bracketSameLine: true,
  singleQuote: true,
  semi: true,
  trailingComma: "es5",
  tabWidth: 2,
  printWidth: 80,
  endOfLine: "auto",
  bracketSpacing: true,
};
```

**ESLint** — if the template shipped a config, leave it; otherwise create `.eslintrc.js` extending `@react-native` (CLI) or `eslint-config-expo` (Expo), installing that shareable config as a devDependency.

> `prettier/prettier` requires `eslint-plugin-prettier` + `eslint-config-prettier` as devDependencies, and Prettier itself must be v3+ (`eslint-plugin-prettier@5` peer-depends on `prettier@>=3`) — the RN CLI template ships Prettier v2, so upgrade it: `npm install --save-dev prettier@^3 eslint-plugin-prettier eslint-config-prettier`. Registering the rule directly under `rules` without adding `plugin:prettier/recommended` to `extends` fails with "Definition for rule 'prettier/prettier' was not found" — always include it.

```javascript
module.exports = {
  root: true,
  extends: ["@react-native", "plugin:prettier/recommended"],
  rules: {
    "prettier/prettier": [
      "error",
      {
        arrowParens: "avoid",
        bracketSameLine: true,
        singleQuote: true,
        semi: true,
        trailingComma: "es5",
        tabWidth: 2,
        printWidth: 80,
        endOfLine: "auto",
        bracketSpacing: true,
      },
    ],
    "react-hooks/exhaustive-deps": "warn",
    "react-native/no-inline-styles": "off",
    "no-unused-vars": "off",
    "@typescript-eslint/no-unused-vars": ["warn", { argsIgnorePattern: "^_" }],
  },
  ignorePatterns: [
    "node_modules/",
    "**/*.test.ts",
    "**/*.test.tsx",
    "**/*.spec.ts",
    "**/*.spec.tsx",
    "**/__tests__/**",
    "**/__mocks__/**",
  ],
};
```

Also create an `.eslintignore` file (for older ESLint tooling that doesn't read `ignorePatterns`) with the same test-file exclusions:

```
node_modules/
**/*.test.ts
**/*.test.tsx
**/*.spec.ts
**/*.spec.tsx
**/__tests__/**
**/__mocks__/**
```

**Husky + lint-staged** — Husky v9 setup:

```bash
git init   # only if the project is not already a git repo (CLI init used --skip-git-init)
npx husky init
```

Overwrite `.husky/pre-commit` with:

```bash
npx lint-staged
```

Add to `package.json`:

```json
{
  "scripts": {
    "prepare": "husky",
    "lint": "eslint .",
    "format": "prettier --write ."
  },
  "lint-staged": {
    "*.{ts,tsx,js,jsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md}": ["prettier --write"]
  }
}
```

Verify the hook works: `npx lint-staged --help` exits 0 and `.husky/pre-commit` exists. Do **not** run a real commit on the user's behalf.

---

## Phase 11 — Update App Entry Point

Overwrite `App.tsx`, composing it from the selected `MODULES`. If the template produced `App.js` instead (e.g. Expo `bare-minimum`), delete it and create `App.tsx`; check `index.js`/`package.json#main` still resolves to the app entry.

Compose the tree from the inside out:

- **Base** (no modules): `<SafeAreaProvider>` wrapping `<ComponentShowcaseScreen />`.
- **If `navigation`**: render `<AppNavigator />` instead of the showcase (the showcase is reachable as the `Showcase` route).
- **If `state` (or `api`, which implies it)**: wrap everything in `<Provider store={store}>` from `react-redux`.

Example with all modules selected:

```typescript
import React from 'react';
import { Provider } from 'react-redux';
import { SafeAreaProvider } from 'react-native-safe-area-context';
import { store } from './src/store';
import AppNavigator from './src/navigation/AppNavigator';

const App: React.FC = () => (
  <Provider store={store}>
    <SafeAreaProvider>
      <AppNavigator />
    </SafeAreaProvider>
  </Provider>
);

export default App;
```

Never import `store` or `AppNavigator` when their module wasn't selected — the file won't compile.
