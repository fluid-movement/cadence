# Cadence — Task Management System

Cadence is a tailor-made task management system built for teams running multiple projects in parallel. It replaces Jira and is designed around how teams actually work — not a generic agile framework.

## Problem Statement

A custom-built system allows:
- Features tailored exactly to the team's workflow (no bloat, no missing pieces)
- Full control over hosting, data, and future development
- A better UX for the PM/team lead planning loop

## Who Uses It

- **Project Managers** — plan versions, monitor project status, manage task backlog
- **Team Leads** — estimate tasks, coordinate with PM, assign work to developers
- **Developers / Designers / Specialists** — execute tasks, log time, update statuses
- **Customers** — submit and track their own requests (limited access via role config)
- **HR / Operations / Other teams** — manage their own task flows within shared projects

## Core Concepts

- **Task** — the fundamental unit of work. Belongs to a project and a team.
- **Project** — a client engagement or internal initiative. Multiple teams work on the same project.
- **Version** — a planned batch of tasks targeted at a specific deploy. Not a time-box.
- **Workflow** — a configurable FSM defining the statuses a task type moves through.
- **Role** — a set of permissions. Everything is deny-by-default; access is granted via roles.

## Key Design Principles

- **Configuration over code** — workflows, fields, permissions, and notification handlers are configured, not hardcoded.
- **Query language as the backbone** — all list views and dashboards are powered by a flexible query language. No hardcoded business logic in the UI.
- **Decoupled notifications** — the app fires events; pluggable handlers deliver them (email, Slack, etc.).
- **Deny by default** — all permissions are off unless explicitly granted. Customers, internal users, and admins are all just roles.

## Tech Stack

SvelteKit + TypeScript + PostgreSQL. See [architecture.md](architecture.md) for the full decision and rationale.

## Documentation Index

- [Roadmap](roadmap.md) — V1 and V2 scope
- [Tasks](features/tasks.md) — task anatomy and fields
- [Workflows](features/workflows.md) — FSM, status transitions, mandatory fields
- [Roles & Permissions](features/roles-permissions.md) — permission model
- [Users, Teams & Groups](features/users-teams-groups.md) — user model
- [Projects & Versions](features/projects-versions.md) — project structure and version planning
- [Query Language](features/query-language.md) — filtering system (TODO)
- [Time Tracking](features/time-tracking.md) — timer, logging, export
- [Notifications](features/notifications.md) — event system and handlers
- [Reporting](features/reporting.md) — dashboards and project views
- [AI & Automation](features/ai-automation.md) — FSM actions, coding agent, MCP server (V2)
- [GitLab Integration](features/gitlab-integration.md) — V2
- [REST API](features/rest-api.md) — V2
- [CLI / TUI](features/cli-tui.md) — V2

## Open Questions
- [questions.md](questions.md) — all unresolved spec decisions, gaps, and feature comparisons
