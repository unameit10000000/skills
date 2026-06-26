---
name: api-design
type: backend
description: Designing or implementing API endpoints, routes, handlers, middleware, or backend services.
---

# API Design

## First Action

**Check `patterns/api/` for existing endpoint patterns.** Adapt the existing pattern rather than inventing a new shape.

## Conventions

- RESTful resource naming
- Consistent error envelope across all endpoints
- Auth middleware on all protected routes
- Input validation on all mutations — **always validate all request body fields are defined before passing to Knex queries. Undefined bindings in Knex produce cryptic errors like "Undefined binding(s) detected when compiling UPDATE". Add explicit checks at the top of every route handler.**
- Repository pattern for DB access (no raw queries in handlers)

## Reference Files

- `rest-conventions.md` — naming, status codes, error format
- `auth-patterns.md` — authentication/authorization patterns

## After Building

If you created a new endpoint pattern (e.g. a new type of handler, a new middleware pattern), document it in `patterns/api/`.
