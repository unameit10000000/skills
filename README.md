<div align="center">

# 🧠 Skills

### An open collection of crafted skills for any AI agent — free for anyone to use.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/unameit10000000/skills/pulls)
[![Skills](https://img.shields.io/badge/skills-4-orange.svg)](#-skills)

</div>

---

## 📖 What is this?

This repository contains a curated set of **skill definitions** — structured markdown files that teach AI coding agents how to approach specific tasks. Each skill is self-contained, opinionated, and battle-tested with real-world gotchas baked in.

Drop any skill folder into your agent's skill directory and it will instantly know:

- **What conventions to follow** (naming, structure, patterns)
- **What mistakes to avoid** (known gotchas and edge cases)
- **Where to look for reference** (linked pattern files)

---

## 🧩 Skills

| Skill | Type | Description |
|---|---|---|
| [`api-design`](./api-design) | `backend` | RESTful API design — endpoint patterns, auth middleware, error envelopes, status codes |
| [`db-schema`](./db-schema) | `database` | Database schema design — migrations, naming conventions, query patterns, indexes |
| [`domain-checker`](./domain-checker) | `utility` | DNS-based domain availability checker for Windows PowerShell |
| [`stack-nextjs-docker-npm`](./stack-nextjs-docker-npm) | `stack` | Full-stack recipe: Next.js 15 + PostgreSQL + Docker Compose (npm, no monorepo) |

---

## 📂 Repository Structure

```
skills/
├── api-design/
│   ├── SKILL.md              ← Skill entry point (frontmatter + conventions)
│   ├── rest-conventions.md   ← REST naming, status codes, error/success envelopes
│   └── auth-patterns.md      ← Authentication & authorization patterns
│
├── db-schema/
│   ├── SKILL.md              ← Skill entry point (frontmatter + conventions)
│   ├── migration-template.md ← Standard Knex migration structure
│   ├── naming-conventions.md ← Table, column, index, FK naming rules
│   └── query-patterns.md     ← N+1 avoidance, pagination, transactions, soft delete
│
├── domain-checker/
│   ├── SKILL.md              ← Skill entry point + usage examples
│   ├── SKILL.md.txt          ← Plain-text copy
│   └── apm.yaml              ← APM package manifest
│
├── stack-nextjs-docker-npm/
│   └── SKILL.md              ← Complete stack guide (versions, Dockerfile, compose, gotchas)
│
└── README.md                 ← You are here
```

---

## 🚀 How to Use

### Option 1 — Use with an AI agent

Copy or symlink any skill folder into your agent's skills directory. The agent reads the `SKILL.md` frontmatter to know **when** to activate the skill, then follows the conventions and reference files inside.

```yaml
# Example frontmatter (from api-design/SKILL.md)
---
name: api-design
type: backend
description: Designing or implementing API endpoints, routes, handlers, middleware, or backend services.
---
```

### Option 2 — Read as reference

Each skill is just structured markdown. Read it directly to absorb the conventions, patterns, and gotchas — no tooling required.

### Option 3 — Use with APM

The `domain-checker` skill includes an [`apm.yaml`](./domain-checker/apm.yaml) manifest for [APM](https://github.com/topics/apm) (AI Package Manager) integration:

```yaml
name: domain-checker
version: 1.0.0
type: skill
```

---

## ✨ Skill Highlights

### `api-design`
- RESTful resource naming with standard CRUD mapping
- Consistent error envelope: `{ success, error: { code, message } }`
- Auth middleware pattern with ownership checks
- Input validation guard against undefined Knex bindings

### `db-schema`
- Snake_case in DB, camelCase in code — repository layer handles mapping
- Every table: `id`, `created_at`, `updated_at` — no exceptions
- Immutable migrations with working rollbacks
- Query patterns: N+1 avoidance, pagination, transactions, soft delete

### `domain-checker`
- One-liner DNS check via `Resolve-DnsName` on Windows PowerShell
- Batch-check 50+ domains in seconds
- No external dependencies — uses built-in PowerShell cmdlets

### `stack-nextjs-docker-npm`
- Next.js 15 standalone output + PostgreSQL 16 + Docker Compose
- 19 documented gotchas (NextAuth Edge Runtime, Knex webpack externals, migration ordering, and more)
- Multi-stage Dockerfile with healthchecks and ordered service startup
- Proven dependency versions to avoid compatibility breakage

---

## 🤝 Contributing

Contributions are welcome! To add a new skill:

1. **Create a folder** named after your skill (e.g., `my-skill/`)
2. **Add a `SKILL.md`** with YAML frontmatter (`name`, `type`, `description`) and your conventions
3. **Add reference files** for patterns, templates, or examples
4. **Open a pull request** — describe what the skill does and when an agent should use it

### Skill types

| Type | Use for |
|---|---|
| `backend` | API design, server logic, middleware |
| `database` | Schema design, migrations, queries |
| `frontend` | UI patterns, component conventions |
| `stack` | Full-stack setup guides, infrastructure |
| `utility` | Standalone tools and scripts |
| `workflow` | Process and methodology guides |

---

## 📄 License

MIT — free for anyone to use, modify, and distribute. See [LICENSE](./LICENSE).

---

<div align="center">

**[⬆ Back to top](#-skills)**

Made with care for the AI agent community.

</div>
