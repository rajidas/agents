# React API Task

Implement the requested API integration using `agents/react-api.agent.md`.

Inspect existing clients, query hooks, auth, response models, error handling, and test utilities. Keep components free of direct HTTP calls; use typed client modules and hooks (React Query/TanStack Query, RTK Query, or a plain typed wrapper). Validate response shapes, protect credentials, handle cancellation and bounded retries where appropriate, and map 400, 401, 403, 404, 409, and 5xx failures to stable errors. Route authorization must not depend only on hidden UI controls.

Add focused tests with MSW for success and relevant failure paths. Run lint, typecheck, unit/integration tests, and build. Report contracts, changed clients/hooks, commands, and residual risk.
