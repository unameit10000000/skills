# Migration Template

## Naming Convention

```
YYYYMMDDHHMMSS_description.ts
```

Example: `20260601120000_create_users_table.ts`

## Structure

```typescript
import type { Knex } from 'knex';

export async function up(knex: Knex): Promise<void> {
  await knex.schema.createTable('table_name', (table) => {
    table.uuid('id').primary().defaultTo(knex.fn.uuid());
    // ... columns
    table.timestamp('created_at').defaultTo(knex.fn.now());
    table.timestamp('updated_at').defaultTo(knex.fn.now());
  });

  // Indexes
  // await knex.schema.raw('CREATE INDEX idx_table_column ON table_name (column)');
}

export async function down(knex: Knex): Promise<void> {
  await knex.schema.dropTableIfExists('table_name');
}
```

## Rules

- Every migration must have a working `down` (rollback)
- Never modify a migration that has already been deployed — create a new one
- Group related changes in one migration (e.g. table + its indexes)
- Always log migrations in `work/logs.md` with: file path, what it creates/alters, rollback SQL
