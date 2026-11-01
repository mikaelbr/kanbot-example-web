---
name: backend-practices
description: Rules for server, API, data and domain changes - input validation, error shape, migrations, backward compatibility, authorisation and secrets. Use when building or changing endpoints, data models, queries or background jobs.
---

# Backend practices

## Before you code

- Map the modules, data access, validation and error patterns the change touches. Reuse what exists.
- Write failing tests first (`tdd`). Cover the unhappy paths: invalid input, missing records, permission denied, conflicts.

## Boundaries

- Validate input at the edge. Return the project's standard error shape.
- Schema changes ship as migrations that are safe on existing data. Say whether they are reversible.
- Public APIs stay backward compatible unless the task says otherwise.
- Parameterised queries only. Authorise every new endpoint and action.
- No secrets, tokens or personal data in logs, fixtures or commits.

## Commits

Small, coherent steps with Conventional Commits (`feat(api): …`, `fix(db): …`, `test: …`).

## Report

- Example request and response for API changes.
- Migration notes for schema changes, and whether they reverse.
- Risks: compatibility, performance, security-sensitive spots.
