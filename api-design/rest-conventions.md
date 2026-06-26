# REST Conventions

> Placeholder — customize for your project. Check `patterns/api/` for project-specific implementations.

## Naming

| Action | Method | Path | Example |
|---|---|---|---|
| List | GET | `/api/{resource}` | `GET /api/users` |
| Get one | GET | `/api/{resource}/:id` | `GET /api/users/123` |
| Create | POST | `/api/{resource}` | `POST /api/users` |
| Update | PATCH | `/api/{resource}/:id` | `PATCH /api/users/123` |
| Delete | DELETE | `/api/{resource}/:id` | `DELETE /api/users/123` |

## Status Codes

| Code | Meaning | When |
|---|---|---|
| 200 | OK | Successful GET, PATCH |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Validation failure |
| 401 | Unauthorized | Not authenticated |
| 403 | Forbidden | Authenticated but not allowed |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Duplicate or state conflict |
| 500 | Internal Error | Unexpected server error |

## Error Envelope

```typescript
// All error responses use this shape:
{
  success: false,
  error: {
    code: "ERR_XXX",        // opaque code (never expose internals)
    message: "Human-readable message"
  }
}
```

## Success Envelope

```typescript
// Single resource:
{ success: true, data: { ... } }

// List:
{ success: true, data: [...], pagination: { page, limit, total } }
```

## Pagination

```typescript
// Query params:
GET /api/users?page=1&limit=20

// Response:
{
  success: true,
  data: [...],
  pagination: {
    page: 1,
    limit: 20,
    total: 142,
    totalPages: 8
  }
}
```
