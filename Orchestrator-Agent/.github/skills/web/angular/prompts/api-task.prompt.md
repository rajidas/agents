# Angular API Task

Implement the requested API integration using `agents/angular-api.agent.md` and `skills/http.skill.md`.

Inspect existing clients, interceptors, auth, response models, error handling, and test utilities. Keep components free of HTTP calls; use injectable typed services. Validate response shapes, protect credentials, handle cancellation and bounded retries where appropriate, and map 400, 401, 403, 404, 409, and 5xx failures to stable errors. Route authorization must not depend only on hidden UI controls.

Add focused tests for success and relevant failure paths. Run lint, typecheck, unit/integration tests, and build. Report contracts, changed services/interceptors, commands, and residual risk.
