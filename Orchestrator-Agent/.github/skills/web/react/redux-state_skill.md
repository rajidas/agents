---
name: redux-state_skill
description: State management for React JS using Redux Toolkit with typed hooks, slices, and RTK Query
---

# State Management Skill — Redux Toolkit

This skill implements scalable state management for React JS projects using **Redux Toolkit (RTK)** and **RTK Query**.

---

## Architecture

```
src/
├── store/
│   ├── index.ts             # Root store configuration
│   ├── hooks.ts             # Typed useAppDispatch and useAppSelector
│   └── rootReducer.ts       # Combined root reducer
└── features/
    └── auth/
        └── store/
            ├── authSlice.ts         # State slice
            └── authApi.ts           # RTK Query API slice
```

---

## Store Setup

```ts
// src/store/index.ts
import { configureStore } from '@reduxjs/toolkit';
import { rootReducer } from './rootReducer';
import { authApi } from '@/features/auth/store/authApi';

export const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(authApi.middleware),
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

```ts
// src/store/hooks.ts
import { useDispatch, useSelector } from 'react-redux';
import type { RootState, AppDispatch } from './index';

export const useAppDispatch = useDispatch.withTypes<AppDispatch>();
export const useAppSelector = useSelector.withTypes<RootState>();
```

---

## Slice Pattern

```ts
// src/features/auth/store/authSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
}

const initialState: AuthState = {
  user: null,
  token: null,
  isAuthenticated: false,
};

export const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    setCredentials: (state, action: PayloadAction<{ user: User; token: string }>) => {
      state.user = action.payload.user;
      state.token = action.payload.token;
      state.isAuthenticated = true;
    },
    logout: (state) => {
      state.user = null;
      state.token = null;
      state.isAuthenticated = false;
    },
  },
});

export const { setCredentials, logout } = authSlice.actions;
export default authSlice.reducer;
```

---

## RTK Query API Slice Pattern

```ts
// src/features/auth/store/authApi.ts
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const authApi = createApi({
  reducerPath: 'authApi',
  baseQuery: fetchBaseQuery({
    baseUrl: import.meta.env.VITE_API_BASE_URL,
    prepareHeaders: (headers, { getState }) => {
      const token = (getState() as RootState).auth.token;
      if (token) headers.set('Authorization', `Bearer ${token}`);
      return headers;
    },
  }),
  endpoints: (builder) => ({
    login: builder.mutation<LoginResponse, LoginRequest>({
      query: (credentials) => ({
        url: '/auth/login',
        method: 'POST',
        body: credentials,
      }),
    }),
    getProfile: builder.query<UserProfile, void>({
      query: () => '/auth/me',
    }),
  }),
});

export const { useLoginMutation, useGetProfileQuery } = authApi;
```

---

## Rules

- Use `useAppDispatch` and `useAppSelector` — never the untyped originals.
- State slices live inside their feature folder.
- API slices use RTK Query — no raw `useEffect` + `fetch` for server state.
- Global UI state (theme, notifications, modal) lives in `src/store/`.
- Server state (API data) is managed exclusively by RTK Query.
- Never store derived data in Redux — compute with selectors.

---

## Capabilities

- Generate full Redux Toolkit store setup for a new project
- Add a new feature slice to an existing store
- Migrate a legacy `useReducer` or Context pattern to Redux Toolkit
- Add RTK Query API slice for a given API endpoint set

---

*This is a skill used by the @REACT_JS_AGENT agent.*
