# Skill: Angular Data Contracts

## Purpose

Define safe Angular domain models, DTOs, mapping, and persistence boundaries.

## Rules

- Keep server/database schemas behind the API boundary; Angular owns only client contracts.
- Use explicit interfaces for requests, responses, domain models, pagination, filters, sorting, and errors.
- Map DTOs to domain models in a data-access service and handle nullable or missing fields deliberately.
- Keep credentials, tokens, private fields, and server-only metadata out of browser models.
- Align contract changes with backend migration and compatibility requirements.
- Avoid normalizing state unless it removes meaningful duplication or matches the selected store.

## Validation

Test mapping, malformed payloads, nullable data, pagination, sorting, relationships, and backward-compatible response handling.
