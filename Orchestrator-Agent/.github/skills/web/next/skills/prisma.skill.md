# Skill: Prisma Generator

## Purpose

Generate or update Prisma schema models, migrations, a development-safe Prisma
Client singleton, and typed server-only data-access functions for a Next.js App
Router application.

## Schema Design

- Use clear singular model names and consistent field naming.
- Add an explicit primary key, timestamps, and a deliberate deletion strategy.
- Represent relationships with foreign keys and matching relation fields.
- Add `@unique` constraints for business identifiers that must not duplicate.
- Add indexes for common filters, sort fields, foreign keys, and compound query
	patterns. Do not add indexes without a query or constraint that needs them.
- Choose nullable fields deliberately. A nullable field should represent a real
	absence of data, not an unknown validation state.
- Use enums only when the values are stable and database support is understood.
- Define referential actions explicitly for dependent records, especially for
	tenant, user, and audit data.

Example shape:

```prisma
model Project {
	id        String   @id @default(cuid())
	name      String
	ownerId   String
	createdAt DateTime @default(now())
	updatedAt DateTime @updatedAt
	owner     User     @relation(fields: [ownerId], references: [id])

	@@index([ownerId, createdAt])
}
```

Adjust the example to the project's database provider and established ID
convention.

## Client and Data Access

- Keep the Prisma client in a server-only module such as `lib/db.ts`.
- Use a singleton in development to avoid exhausting connections during hot
	reloads. Follow the project's runtime and deployment model for production.
- Never import Prisma into a Client Component or expose the client in a response.
- Put feature-specific queries in a repository or server module owned by that
	feature, not in page components.
- Select only fields needed by the use case and map records to DTOs before
	returning them to UI or API layers.
- Keep authorization filters in every query for user- or tenant-owned data.

## Transactions and Consistency

- Use `$transaction` when multiple writes must succeed or fail together.
- Keep transactions short and avoid network calls inside a transaction.
- Define uniqueness and conflict behavior at the database level, then map known
	constraint errors to a stable application or API error.
- Use idempotency keys for retryable operations that create external side effects.
- Do not use a cached read as proof that a write is authorized or complete.

## Migration Workflow

1. Inspect existing schema, provider, migration history, and seed conventions.
2. Update `schema.prisma` with the smallest model or constraint change.
3. Run `prisma format` and `prisma validate`.
4. Create a named development migration with `prisma migrate dev`.
5. Review the generated SQL for destructive changes, locks, data loss, and
	 missing indexes.
6. Run `prisma generate`, seed data if required, and execute data-access tests.
7. Document backfill or deployment sequencing for changes that cannot be applied
	 atomically.

Never use `db push` as a replacement for committed migrations in shared or
production environments.

## Security and Reliability

- Validate inputs before they reach Prisma.
- Never construct raw SQL from user input. If raw SQL is necessary, use the
	Prisma parameterized APIs and document why.
- Avoid returning passwords, tokens, internal permissions, or sensitive audit
	fields in general-purpose queries.
- Bound list queries with pagination and deterministic ordering.
- Handle connection, timeout, not-found, and unique-constraint failures without
	leaking database details.

## Testing Expectations

- Schema validation and generated client succeed.
- Migrations are committed and reviewed for safety.
- Constraints and indexes match domain behavior and real queries.
- Repository tests cover important filters, relationships, transactions, and
	conflict cases.

## Completion Checklist

- Server-only boundaries are preserved.
- Authorization is applied to reads and writes.
- DTOs exclude private database fields.
- Seed and reset commands are documented for local and CI environments.