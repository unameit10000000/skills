---
name: db-schema
type: database
description: Database schema design, migrations, queries, indexes, data modeling, or table design.
---

# DB Schema

## First Action

**Check `patterns/db/` for existing query and migration patterns.** Adapt rather than reinvent.

## Conventions

- Snake_case for all DB objects (tables, columns, indexes)
- camelCase in application code — repository layer handles mapping
- Every table has `id`, `created_at`, `updated_at` — **both timestamps are mandatory on every table from migration 1, no exceptions**
- Foreign keys explicitly named: `fk_{table}_{column}`
- **Before making any FK column NOT NULL, check the feature spec.** If the feature allows unauthenticated access (e.g., "no auth required for core feature"), all user FK columns must be nullable. NOT NULL on an optional FK causes runtime constraint violations when unauthenticated users submit.
- Indexes explicitly named: `idx_{table}_{column(s)}`
- Migration file names use current date in YYYYMMDD format (e.g., 20260606_01_create_users.ts)
- **Migration files are immutable once applied** — never modify an existing migration file. Create a new additive migration for any schema change.

## Reference Files

- `migration-template.md` — standard migration structure
- `naming-conventions.md` — full naming rules
- `query-patterns.md` — common query shapes

## After Building

If you created a new query pattern (join shape, pagination approach, transaction scope), document it in `patterns/db/`.
