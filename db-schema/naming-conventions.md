# DB Naming Conventions

## Tables

- Plural, snake_case: `users`, `user_profiles`, `vacancy_applications`
- Junction tables: `{table1}_{table2}` alphabetically: `roles_users`

## Columns

- snake_case: `first_name`, `created_at`, `is_active`
- Primary key: always `id`
- Foreign keys: `{referenced_table_singular}_id` — e.g. `user_id`, `vacancy_id`
- Booleans: prefix with `is_` or `has_` — e.g. `is_active`, `has_verified_email`
- Timestamps: `created_at`, `updated_at`, `deleted_at` (soft delete)

## Indexes

- `idx_{table}_{column}` — single column: `idx_users_email`
- `idx_{table}_{col1}_{col2}` — composite: `idx_applications_user_id_vacancy_id`
- `uniq_{table}_{column}` — unique: `uniq_users_email`

## Foreign Keys

- `fk_{source_table}_{target_table}` — e.g. `fk_applications_users`

## Enums

- Stored as `varchar` with CHECK constraint, or as a separate lookup table
- Application-side enum maps to DB values

## In Code (camelCase)

The repository layer handles the mapping:
- DB: `first_name`, `created_at`, `is_active`
- Code: `firstName`, `createdAt`, `isActive`
