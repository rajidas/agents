# Vue.js State Management

Use Pinia for shared client state when already selected or required; use composables and local refs/reactive state for local UI state. Keep state typed, expose readonly state where possible, and separate server state from UI state.

Test state transitions, loading, success, empty, error, unauthorized, and optimistic-update recovery where applicable. Never store secrets in browser state. Preserve the existing state library in an existing application and do not introduce a competing solution without a documented decision.
