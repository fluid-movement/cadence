# Cadence

A task management system built for teams running multiple projects in parallel. Designed around how teams actually work — not a generic agile framework.

## Core concepts

- **Tasks** — the fundamental unit of work, belonging to a project and a team
- **Projects** — client engagements or internal initiatives with multiple teams
- **Versions** — planned batches of tasks targeted at a specific deploy (not a time-box)
- **Workflows** — configurable FSMs defining the statuses a task type moves through
- **Roles** — deny-by-default permission sets; all access is explicitly granted

## Stack

| Layer | Technology |
|---|---|
| Frontend | SvelteKit + Svelte 5 |
| UI Components | shadcn-svelte |
| Backend | SvelteKit server routes + TypeScript service layer |
| Database | PostgreSQL |
| ORM | Drizzle |
| Job Queue | pg-boss (PostgreSQL-backed, no Redis) |
| Workers | Separate Node.js process (`worker.ts`) |
| Email | Nodemailer (SMTP) |

Two processes, one codebase, one PostgreSQL instance.

## Project structure

```
src/
  routes/          ← SvelteKit routes (thin — validate input, call service)
  lib/
    services/      ← Domain logic (FSM engine, permissions, notifications, time tracking)
    db/            ← Drizzle schema and query helpers
    queue/         ← Job definitions and enqueue helpers (pg-boss)
    mcp/           ← MCP server (V2)
  worker.ts        ← Worker entry point
docs/              ← Requirements and feature specs
```

## Documentation

- [`docs/main.md`](docs/main.md) — project overview
- [`docs/roadmap.md`](docs/roadmap.md) — V1 vs V2 scope
- [`docs/architecture.md`](docs/architecture.md) — stack rationale and architectural decisions
- [`docs/features/`](docs/features/) — individual feature specs

## Status

Currently in the **planning phase**. See the [roadmap](docs/roadmap.md) for V1 scope.
