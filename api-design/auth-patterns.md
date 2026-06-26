# Auth Patterns

> Placeholder — fill in with your project's auth approach at project start.

## Authentication Flow

```
Client → gets token (login/OAuth) → sends in Authorization header → server verifies
```

## Middleware Pattern

```typescript
// Example — adapt to your auth provider:
async function requireAuth(request: Request): Promise<AuthResult> {
  const token = request.headers.get('Authorization')?.replace('Bearer ', '');
  if (!token) return { authenticated: false, error: 'No token' };

  // Verify with your auth provider
  const decoded = await verifyToken(token);
  return { authenticated: true, uid: decoded.uid, email: decoded.email };
}
```

## Route Protection

```typescript
// Every protected route starts with:
const auth = await requireAuth(request);
if (!auth.authenticated) {
  return Response.json({ success: false, error: { code: 'ERR_AUTH', message: 'Unauthorized' } }, { status: 401 });
}
```

## Ownership Check

```typescript
// For mutations on user-owned resources:
const resource = await repo.getById(id);
if (resource.uid !== auth.uid) {
  return Response.json({ success: false, error: { code: 'ERR_FORBIDDEN', message: 'Forbidden' } }, { status: 403 });
}
```

## Rules

- Never trust client-side auth state for server decisions
- Always verify the token server-side
- Always check ownership on mutations
- Never expose tokens in logs or error responses
- Document your auth provider choice in `work/codex.md`
