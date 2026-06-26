---
name: stack-nextjs-docker-npm
type: stack
description: Simpler single-app stack using npm. Next.js 15 (standalone) + PostgreSQL + Docker Compose. No monorepo, no separate API process — Next.js API routes handle the backend. Uses npm instead of pnpm to avoid Docker Alpine symlink issues.
---

# Stack: Next.js + Docker with npm (No Monorepo)

## When to Use

Pick this stack when:
- Single app, no separate API service needed (Next.js API routes are sufficient)
- Simpler deployment surface
- Deployment target is VPS / Docker (not Vercel)
- Team size: solo or small
- You want to avoid pnpm + Docker Alpine compatibility issues

For pnpm-based setup, use `stack-nextjs-docker` instead.

---

## Structure

```
project-root/
├── app/              ← Next.js App Router
├── components/
├── lib/
├── public/
├── Dockerfile
├── docker-compose.yml
├── next.config.ts    ← output: standalone
├── package.json
└── tsconfig.json
```

---

## Versions (proven)

| Dependency | Version |
|---|---|
| Node.js (Docker) | 22-alpine |
| npm | latest (bundled with Node) |
| Next.js | ^15.0.3 |
| React | ^19.0.0 |
| TypeScript | ^5 |
| Tailwind CSS | ^3.4.x |
| PostgreSQL | 16-alpine |
| NextAuth | beta (v5) |

**Use npm instead of pnpm** — npm has no symlink issues with Docker Alpine and requires no extra configuration. pnpm requires `.npmrc` with `shamefully-hoist=true` and additional setup steps.

**Use Tailwind 3.4.x — not 4.x.** Tailwind 4 changes the config model entirely and breaks shadcn/ui compatibility.

---

## Key Config Files

### `next.config.ts`
```ts
const nextConfig: NextConfig = {
  output: "standalone",
  webpack: (config, { isServer }) => {
    if (isServer) {
      config.externals = [
        ...(config.externals || []),
        'pg-native', 'mysql', 'mysql2', 'oracledb', 'sqlite3', 'tedious', 'better-sqlite3',
      ];
    }
    return config;
  },
};
export default nextConfig;
```
`output: "standalone"` is required for Docker. The run command is `node .next/standalone/server.js`, NOT `next start`.

The webpack externals block is required when using Knex — Next.js otherwise tries to bundle all Knex database dialects, causing build errors for modules that aren't installed.

### `.dockerignore` (required)
```
node_modules
.next
.git
```
Prevents local build artifacts from conflicting with Docker build context.

### `Dockerfile` (multi-stage — npm)
```dockerfile
FROM node:22-alpine AS builder
WORKDIR /app
ENV NEXT_TELEMETRY_DISABLED=1
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:22-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
ENV HOSTNAME=0.0.0.0
ENV NEXT_TELEMETRY_DISABLED=1
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/scripts ./scripts
COPY --from=builder /app/migrations ./migrations
COPY --from=builder /app/seeds ./seeds
COPY --from=builder /app/knexfile.ts ./knexfile.ts
EXPOSE 3000
CMD ["node", "server.js"]
```

**Use `npm install` instead of `npm ci`** — Less strict, suitable for development, avoids lock file sync issues when dependencies are added incrementally.

**Copy full node_modules and scripts/migrations/seeds** — Next.js standalone output excludes knex, pg, tsx even when in dependencies. Copying full node_modules and runtime files ensures migration/seed services work.

---

## Docker Compose Pattern

```yaml
services:
  postgres:
    image: postgres:16-alpine
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U <user> -d <db>"]
      interval: 5s
      retries: 10
    environment:
      POSTGRES_USER: <user>
      POSTGRES_PASSWORD: <password>
      POSTGRES_DB: <db>
    volumes:
      - pgdata:/var/lib/postgresql/data

  migrate:
    build: .
    command: ["npx", "tsx", "scripts/migrate.ts"]
    depends_on:
      postgres: { condition: service_healthy }
    env_file: .env
    environment:
      DATABASE_URL: postgres://<user>:<password>@postgres:5432/<db>
    restart: "no"

  seed:
    build: .
    command: ["npx", "tsx", "scripts/seed.ts"]
    depends_on:
      postgres: { condition: service_healthy }
      migrate: { condition: service_completed_successfully }
    env_file: .env
    environment:
      DATABASE_URL: postgres://<user>:<password>@postgres:5432/<db>
    restart: "no"

  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      postgres: { condition: service_healthy }
      seed: { condition: service_completed_successfully }
    env_file: .env
    environment:
      DATABASE_URL: postgres://<user>:<password>@postgres:5432/<db>

volumes:
  pgdata:
```

**Use custom migration/seed scripts** — Don't use Knex CLI (`npx tsx node_modules/.bin/knex`). Create `scripts/migrate.ts` and `scripts/seed.ts` that import knex programmatically. Avoids CLI path resolution issues in Docker.

**Healthcheck must use correct database name** — Include `-d <db>` in `pg_isready` to match the actual database name defined in POSTGRES_DB.

---

## Run Commands

| Command | What it does |
|---|---|
| `docker compose up --build` | Build + start all services |
| `docker compose down` | Stop all services |
| `docker compose logs -f app` | Follow app logs |

---

## Known Gotchas

1. **`output: standalone` ≠ `next start`** — In Docker, the runner command is `node server.js` (in the standalone dir). `next start` only works without standalone output.

2. **Static assets must be copied manually in Dockerfile** — The standalone output does NOT include `.next/static` or `public/`. Both must be explicitly `COPY`'d into the runner stage.

3. **Migration file ordering** — If multiple migrations share the same date prefix, they sort alphabetically. Use `YYYYMMDD_NN_name` to force order when FK dependencies exist.

4. **Seed data must match migration column types** — UUID columns require actual UUIDs in seed data, not `'1'`, `'2'` etc.

5. **`shadcn init` must not be bypassed** — Run `npx shadcn@latest init` to generate the correct `globals.css`. Creating `components.json` manually produces an incomplete setup.

6. **SessionProvider in App Router** — If using NextAuth with `useSession`, wrap the layout in a `'use client'` Providers component. Cannot be added inline to `app/layout.tsx`.

7. **NextAuth middleware must not import `auth()` directly** — Next.js middleware runs in Edge Runtime. Use `getToken` from `next-auth/jwt` instead.

8. **Never import Knex inside NextAuth callbacks** — Auth callbacks run in Edge Runtime. Move all DB operations into a dedicated API route.

9. **Always add `jwt` + `session` callbacks to propagate DB user ID** — Without these, `session.user.id` will be `undefined` in API routes.

10. **Always add an explicit `redirect` callback** — Without it, NextAuth's default redirect logic may not respect the post-login destination.

11. **Implement user sync on every sign-in** — OAuth users exist in the session but NOT in your PostgreSQL `users` table.

12. **DATABASE_URL in Docker must use service name** — Inside Docker Compose, use `postgres` (service name) as the hostname, not `localhost`.

13. **public/ directory must exist** — Next.js requires a public/ directory. Run `mkdir public` immediately if manually scaffolding.

14. **Runtime deps in dependencies** — knex, pg, tsx must be in `dependencies` (not `devDependencies`) since they're needed by migration/seed scripts in Docker.

15. **Seed scripts must be idempotent** — Always use `.onConflict('id').ignore()` or check existence before inserting.

16. **NextAuth v5 requires trustHost** — Add `trustHost: true` to NextAuth config in `lib/auth.ts` for development/production with localhost.

17. **Disable Next.js telemetry in Dockerfile** — Add `ENV NEXT_TELEMETRY_DISABLED=1` to both the builder and runner stages.

18. **Webpack tries to bundle all Knex dialects** — Add webpack `externals` for all unused dialects in `next.config.ts`.

19. **Verify .env before first build** — Check that DATABASE_URL format and database name match docker-compose.yml healthcheck.
