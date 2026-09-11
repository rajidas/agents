# Skill: Angular Application Architecture

## Purpose

Define maintainable Angular architecture for standalone, feature-based applications while preserving existing conventions.

## Principles

- Use strict TypeScript, standalone components, Angular Router, dependency injection, RxJS, and Signals.
- Keep presentation, routing, application use cases, transport, and persistence contracts separate.
- Organize by feature; keep shared primitives in `shared` and app-wide providers in `core`.
- Prefer OnPush change detection, typed reactive forms, and explicit input/output contracts.
- Keep route components thin: compose feature views and delegate behavior to services or facades.
- Keep dependencies directed inward: components call facades/services, services call API clients, and API clients own transport.

## Recommended Structure

```text
src/app/
  core/                 # bootstrap providers, auth, global interceptors, error handling
  shared/               # reusable components, directives, pipes, validators
  layouts/              # shell and dashboard layouts
  features/             # feature-owned components, pages, services, models
  services/             # shared application services only
  guards/               # route access policies
  models/               # shared public types and DTOs
  interceptors/         # cross-cutting HttpClient behavior
  state/                # shared state when a store is selected
  app.routes.ts
  app.config.ts
```

Feature-specific code belongs under `features/<feature>` rather than a global utility folder. Keep API models separate from domain view models and map between them.

## Data Flow

```text
Router -> page component -> facade/service -> typed API client -> HTTP API
       <- view model <- state stream/signal <- mapped response
```

Do not place database access, secrets, or server authorization in Angular code. Guards improve navigation but do not replace server authorization.

## Validation

Test route boundaries, service behavior, state transitions, mapping, errors, accessibility, and production builds. Preserve the selected state library and existing project style.
