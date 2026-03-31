# Architecture

## Stack Decision

**SvelteKit + TypeScript + PostgreSQL**

Decided after evaluating PHP/Symfony, Go, and SvelteKit. Key factors:
- Team is proficient in Svelte; strong frontend capability
- TypeScript is the primary language for the Anthropic/Claude SDK and MCP SDK — natural fit for the V2 AI features
- Node.js is faster than PHP, sufficient for this scale
- Single codebase, one language across frontend and backend
- shadcn-svelte provides a strong, consistent UI component base

---

## Full Stack

| Layer | Technology | Notes |
|---|---|---|
| Frontend | SvelteKit + Svelte 5 | SSR, routing, UI |
| UI Components | shadcn-svelte | Accessible, unstyled-base component library |
| Backend | SvelteKit server routes + TypeScript service layer | Thin routes, fat services |
| Database | PostgreSQL | Primary data store |
| ORM | Drizzle | Lightweight, excellent TypeScript types |
| Job Queue | pg-boss | PostgreSQL-backed queue — no Redis required |
| Workers | Separate Node.js process (`worker.ts`) | Same repo, shared types with web app |
| Email | Nodemailer | SMTP-based, V1 notification handler |
| MCP Server | TypeScript MCP SDK | TypeScript is the primary MCP SDK language |
| LLM Integration | Anthropic TypeScript SDK | For V2 AI features |

---

## Process Architecture

Two processes, one codebase, one deployment:

```
┌─────────────────────┐     ┌──────────────────────┐
│   Web Process       │     │   Worker Process      │
│   (SvelteKit)       │     │   (worker.ts)         │
│                     │     │                       │
│  - HTTP server      │     │  - FSM automation     │
│  - API routes       │────▶│  - LLM API calls      │
│  - Auth             │queue│  - Notification       │
│  - CRUD             │     │    dispatch            │
│  - FSM transitions  │     │  - GitLab API calls   │
│  - Enqueue jobs     │     │  - Scheduled jobs     │
└─────────────────────┘     └──────────────────────┘
           │                           │
           └───────────┬───────────────┘
                       │
               ┌───────▼───────┐
               │  PostgreSQL   │
               │               │
               │  - App data   │
               │  - Job queue  │
               │    (pg-boss)  │
               └───────────────┘
```

**Why pg-boss over Redis/BullMQ:** The job queue runs on PostgreSQL, which is already required. This eliminates Redis as a dependency, keeping the self-hosted deployment simple — one database, two processes.

---

## Application Layers

SvelteKit routes are kept thin. All domain logic lives in a TypeScript service layer that both the web routes and workers can import.

```
src/
  routes/          ← SvelteKit routes (thin — validate input, call service, return response)
  lib/
    services/      ← Domain logic (FSM engine, permissions, notifications, time tracking…)
    db/            ← Drizzle schema and query helpers
    queue/         ← Job definitions and enqueue helpers (pg-boss)
    mcp/           ← MCP server (V2)
  worker.ts        ← Worker entry point — registers job handlers, starts pg-boss worker
```

---

## Key Architectural Decisions

### FSM Engine
No third-party FSM library. The FSM engine is a TypeScript service (~200-300 lines) that:
- Loads workflow configuration from the database
- Validates whether a transition is allowed (role check, mandatory fields check, dependency checks)
- Executes the transition (update status, write activity log entry)
- Enqueues any automation actions defined on the transition

### Permission Checks
A `PermissionService` that takes a user + action + resource and returns allow/deny. Called at the service layer, not just at the route level. Routes enforce it at the boundary; services enforce it internally for actions triggered by workers or automation.

### Real-Time (Timer UI)
The live timer in the task view uses **Server-Sent Events (SSE)**, which SvelteKit supports natively. The timer state is client-side; only the final log entry is persisted when stopped.

### REST API (V2)
SvelteKit API routes (`/api/v1/...`) serve as the REST API. If the routes become too complex, **Hono** can be added alongside SvelteKit as a dedicated API router — it's lightweight and TypeScript-native.

---

## Infrastructure (Self-Hosted)

Minimal footprint by design:

- **One PostgreSQL instance** — app data + job queue
- **Two Node.js processes** — web server + worker (can run on the same machine)
- **File storage** — local filesystem or S3-compatible (for attachments)
- **SMTP server** — existing company mail server for email notifications

### Process Management

> **TODO: decide before first deployment**

Both Node.js processes (`web` and `worker`) need a supervisor to restart them on crash and survive server reboots. Options:

**Option A: PM2** — Node-native process manager. Simplest setup, good fit for a VPS deployment.
```bash
pm2 start server.js --name web
pm2 start worker.js --name worker
pm2 save      # persist process list across reboots
pm2 startup   # hook into systemd/init automatically
```
Handles auto-restart, log aggregation, and reboot persistence out of the box.

**Option B: systemd** — manage both processes as native Linux services via `.service` files. More idiomatic on Linux servers, integrates with existing server monitoring. More setup than PM2 but no extra dependency.

**Option C: Docker Compose** — containerize both processes, use Docker's built-in `restart: unless-stopped` policy. Cleanest option if the team is already containerizing the deployment.

Recommendation: **PM2** for a simple VPS, **Docker Compose** if containerizing.

No Redis. No message broker. No separate search index (full-text search via PostgreSQL `tsvector`).

---

## What Was Ruled Out

| Option | Reason |
|---|---|
| PHP / Symfony | Team moving toward JS/TS; Symfony Workflow component would have helped with FSM but not worth the stack split |
| Go + Templ | High performance but Go expertise is limited; productivity loss outweighs performance gain at this scale |
| Go API + Svelte SPA | Two codebases, two languages, more deployment complexity |
| Redis + BullMQ | Extra infrastructure dependency; pg-boss covers the use case with existing PostgreSQL |
| Separate frontend/backend repos | Unnecessary complexity for this team size and scale |
