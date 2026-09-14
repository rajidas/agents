# Skill: Angular HttpClient Integration

## Purpose

Create typed, testable Angular HTTP clients and transport boundaries.

## Flow

```text
Component/facade -> injectable API service -> HttpClient -> interceptor -> API
```

Keep components free of HTTP calls. Define request and response types, map transport DTOs to domain view models, validate external data when the project has a schema validator, and expose stable errors to feature services.

## Rules

- Inspect existing base URLs, providers, interceptors, auth, and response conventions first.
- Use `HttpClient` with typed methods and explicit query parameters.
- Keep tokens and credentials out of logs, source, and client models.
- Use auth and refresh interceptors only according to the existing authentication policy.
- Handle cancellation, retry, timeout, and backoff deliberately; do not retry unsafe mutations blindly.
- Map known HTTP statuses and unexpected failures to safe errors.

## Testing

Use the project's HTTP testing utilities to cover requests, headers, query parameters, mapping, success, malformed data, auth failure, forbidden access, not-found, conflicts, and server/network errors.
