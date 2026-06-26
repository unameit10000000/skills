# Query Patterns

> Placeholder — document your project's actual query patterns in `patterns/db/` as they're built.

## N+1 Avoidance

```typescript
// BAD: N+1
const users = await repo.getAllUsers();
for (const user of users) {
  user.profile = await repo.getProfileByUserId(user.id); // N queries!
}

// GOOD: Join or batch
const usersWithProfiles = await knex('users')
  .leftJoin('user_profiles', 'users.id', 'user_profiles.user_id')
  .select('users.*', 'user_profiles.bio', 'user_profiles.avatar_url');
```

## Pagination

```typescript
async function paginate(table: string, page: number, limit: number) {
  const offset = (page - 1) * limit;
  const [{ count }] = await knex(table).count('* as count');
  const data = await knex(table).limit(limit).offset(offset).orderBy('created_at', 'desc');
  return { data, pagination: { page, limit, total: Number(count), totalPages: Math.ceil(Number(count) / limit) } };
}
```

## Transaction Scope

```typescript
// Use transactions for multi-table mutations:
await knex.transaction(async (trx) => {
  const userId = await trx('users').insert({ ... }).returning('id');
  await trx('user_profiles').insert({ user_id: userId, ... });
});
// If any statement fails, all are rolled back.
```

## Soft Delete

```typescript
// Never hard-delete user data. Use soft delete:
await knex('users').where({ id }).update({ deleted_at: knex.fn.now() });

// All queries filter soft-deleted records:
const activeUsers = await knex('users').whereNull('deleted_at');
```

## Rules

- Always use parameterized queries (Knex handles this)
- Always paginate list endpoints — no unbounded SELECTs
- Always use transactions for multi-table writes
- Document new query patterns in `patterns/db/`
