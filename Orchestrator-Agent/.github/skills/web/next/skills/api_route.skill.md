# Skill: Next.js API Route Generator

## Purpose

Generate maintainable Next.js App Router route handlers in `app/**/route.ts`.
Use this skill for external HTTP consumers, webhooks, integrations, and API
endpoints that must expose an explicit HTTP contract.

## Route Handler Structure

Keep the handler as a transport adapter. The recommended flow is:

```text
Request
	-> parse path, query, and body
	-> authenticate and authorize
	-> validate input
	-> call application service
	-> map result to response
```

```text
app/api/projects/route.ts              # GET collection, POST creation
app/api/projects/[projectId]/route.ts # GET, PATCH, DELETE one resource
lib/http.ts                            # Shared response helpers, if needed
features/projects/project.service.ts  # Business rules
features/projects/project.schema.ts   # Request schemas
```

Do not place database queries, reusable business rules, or large transformations
directly in `route.ts`.

## HTTP Contract

- Export only the methods supported by the endpoint: `GET`, `POST`, `PUT`,
	`PATCH`, or `DELETE`.
- Use `NextRequest` only when cookies, headers, or URL helpers are needed;
	otherwise use the standard `Request` type.
- Parse route parameters and query values explicitly. Treat all request data as
	untrusted strings until validated.
- Use JSON response bodies with a stable shape, for example `{ data }` for
	success and `{ error: { code, message, details } }` for failures.
- Return `200` for successful reads or updates, `201` for creation, `204` for a
	successful empty deletion, `400` for malformed input, `401` for missing
	authentication, `403` for insufficient permission, `404` for missing
	resources, `409` for conflicts, and `500` only for unexpected failures.
- Do not return a successful status when a mutation failed.

## Validation and Authentication

- Validate JSON bodies with the project's established schema library, such as
	Zod, before calling the service layer.
- Validate query strings, path parameters, pagination limits, sort fields, and
	enum values as well as request bodies.
- Authenticate before reading protected records. Authorize the requested
	resource and action on the server for every protected operation.
- Avoid leaking whether a protected resource exists when the project's security
	policy requires a uniform `404` response.
- For webhooks, verify the signature against the raw request body before parsing
	it, and handle replay protection and idempotency where required.

## Errors and Observability

- Map known domain errors to safe public error codes and HTTP statuses.
- Log unexpected server failures with a request or trace identifier, but return
	a generic message to the client.
- Never return stack traces, SQL messages, tokens, passwords, or private fields.
- Include enough context in logs to diagnose failures without logging sensitive
	request bodies or credentials.
- Keep error handling consistent with existing middleware and response helpers.

## Data Access and Caching

- Keep Prisma and other server-only database clients outside the route handler's
	public boundary.
- Select only fields needed for the API response and map records to DTOs.
- Use pagination for unbounded collections and cap client-controlled limits.
- Make cache behavior explicit for user-specific data. Do not share cached
	responses across users or tenants without a safe cache key.
- Invalidate affected paths or tags only after a mutation succeeds.

## Generation Steps

1. Confirm the resource name, supported methods, authentication policy, and
	 response contract.
2. Create or reuse request schemas and a feature application service.
3. Add the route handler with explicit parsing, validation, authorization, and
	 status codes.
4. Add safe DTO mapping and consistent known-error handling.
5. Add tests for success, invalid input, unauthenticated access, forbidden
	 access, missing resources, conflicts, and unexpected failures as applicable.
6. Run the project's typecheck, lint, and route/API test commands.

## Completion Checklist

- The handler is thin and delegates business logic.
- Every input is validated before use.
- Authentication and authorization are enforced server-side.
- Status codes and error bodies match the documented contract.
- Secrets and private database fields stay server-side.
- Collection endpoints have bounded pagination.
- Tests cover both success and failure paths.