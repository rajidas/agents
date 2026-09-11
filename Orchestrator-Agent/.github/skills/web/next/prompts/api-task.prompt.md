# Next.js API Task

Implement the requested API or server mutation using `agents/nextjs-api.agent.md`.

Apply `skills/architecture.skill.md` and `skills/api_route.skill.md`. Apply
`skills/prisma.skill.md` when the change includes Prisma models or persistence.

## Implementation Requirements

- Inspect existing route, service, validation, authentication, response, and
	error-handling conventions before editing.
- Keep `app/**/route.ts` handlers thin: parse the request, authenticate,
	authorize, validate, call the application service, and map the response.
- Validate path parameters, query strings, headers, and JSON bodies before use.
- Keep domain rules independent of `Request`, `NextRequest`, and
	`NextResponse` objects.
- Use stable success and error response shapes with explicit HTTP status codes:
	`201` for creation, `204` for empty deletion, `400` invalid input, `401`
	unauthenticated, `403` forbidden, `404` missing, and `409` conflicts where
	applicable.
- Keep Prisma, secrets, private environment variables, and server-only SDKs out
	of UI and client bundles.
- Select only required fields, map records to DTOs, and enforce user or tenant
	filters in every protected query.
- Bound collection pagination and define cache/revalidation behavior deliberately.
- For webhooks, verify signatures against the raw body and handle idempotency or
	replay protection where required.

## Validation and Report

Add focused tests for success, invalid input, authentication failure,
authorization failure, missing resources, conflicts, and persistence or server
failure as applicable. Run the project's lint, typecheck, API/unit/integration,
and build commands. Return changed handlers/services/schemas, the response
contract, commands with pass/fail results, and residual risk.