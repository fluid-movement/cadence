# Cadence — Project Instructions

## Status

This project is in the **planning phase**. No implementation code should be written until the requirements documentation is complete and approved.

## Documentation

All requirements and feature specs live in the `docs/` folder:

- `docs/main.md` — project overview and documentation index
- `docs/roadmap.md` — V1 vs V2 scope breakdown
- `docs/features/` — individual feature specs

Before making any implementation decisions, read the relevant docs files. They are the source of truth for requirements.

## Tech Stack

**Decided: SvelteKit + TypeScript + PostgreSQL**

- Frontend: SvelteKit + Svelte 5 + shadcn-svelte
- Backend: SvelteKit server routes + TypeScript service layer
- Database: PostgreSQL with Drizzle ORM
- Job queue: pg-boss (PostgreSQL-backed, no Redis)
- Workers: separate Node.js process (`worker.ts`), same repo as web app

See `docs/architecture.md` for full rationale and architectural decisions.

## Planning Conventions

- All features must have a V1 / V2 / TODO label
- TODOs mean the area needs a dedicated spec session — do not make assumptions and implement
- Docs are updated as decisions are made in planning sessions
