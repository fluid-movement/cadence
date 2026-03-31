# REST API

> **STATUS: V2 — not in scope for initial release**

## Overview

A full REST API exposing all Cadence application functionality. Intended to enable:
- The CLI/TUI tool (see [cli-tui.md](cli-tui.md))
- Third-party integrations
- Custom automation scripts
- Potential future mobile or alternative frontend

## Design Principles

- All V1 features accessible via API
- Authentication via API tokens (per user)
- Same permission model as the web UI — API tokens are scoped to user roles
- JSON request/response

## Planned Endpoints (High Level)

- Tasks (CRUD, FSM transitions, time logging)
- Projects and versions
- Users, teams, user groups
- Roles and permissions (read)
- Workflows and task types (read)
- Saved filters
- Notifications

## Open Questions

- Authentication: API tokens only, or also OAuth2?
- Rate limiting: needed for self-hosted? Probably not, but configurable.
- Versioning strategy: `/api/v1/...`
- OpenAPI/Swagger spec generated or hand-written?
