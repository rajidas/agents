# Skill: Angular State Management

## Purpose

Design predictable state with Angular Signals, RxJS services, or the selected store library.

## Selection

- Signals: local synchronous UI state and small feature state.
- RxJS services: asynchronous workflows and shared streams.
- NgRx, NGXS, or Akita: complex cross-feature state, effects, and devtools requirements.

Preserve the repository's existing choice when one exists.

## Rules

- Separate server state, UI state, form state, and durable browser state.
- Define typed state, selectors/computed values, loading/error transitions, and explicit mutations.
- Cancel stale requests and prevent duplicate writes or stale responses.
- Keep effects at the boundary and keep components focused on rendering and user intent.
- Never store secrets or treat client state as authorization.

## Testing

Cover initial, loading, success, empty, failure, retry, cancellation, optimistic rollback, and mutation transitions with deterministic test data.
