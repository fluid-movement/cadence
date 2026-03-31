# Reporting

## V1 — Core Reporting

### Task List Views

All task lists are powered by the query language. Any user can:
- View a task list filtered by any combination of fields
- Save a filter as a personal saved view (named, bookmarkable URL)
- Switch between saved views quickly

This is the primary day-to-day interface for PMs, team leads, and developers. No special "dashboard" is needed for simple queries — the task list is the dashboard.

### Project Status View

Each project has a status view giving the PM a quick overview:

- Current active version: tasks by status, estimated vs. logged hours
- Per-team breakdown: how many tasks open/in progress/done
- Version history: list of shipped versions with completion stats
- Backlog size: how many unversioned tasks exist

This view is read-only and auto-generated from task data — no manual input required.

### Version Planning View

See [projects-versions.md](projects-versions.md) for the version planning UX. From a reporting perspective:

- Total estimated hours vs. total logged hours per version
- Per-team hour breakdown
- Task completion percentage

---

## V2 — Extended Reporting

### Kanban Board

- Tasks displayed as cards in columns by status
- Drag-and-drop to trigger FSM transitions (subject to transition rules)
- Filterable by team, assignee, version

### Gantt Diagram

- Tasks plotted on a timeline by due date and version
- Shows dependencies (if implemented)
- Read-only in V1 of Gantt; interactive scheduling is a future consideration

### Extended Dashboards

- Cross-project reporting (agency-wide view)
- Team velocity and throughput over time
- Custom report builder

---

## Saved Filters

- Saved filters are **personal** — not shared with other users
- A saved filter is a named URL (query language params)
- Users can have as many saved filters as they like
- Filters appear in the sidebar or a quick-access menu

> **Note:** Shared/team filters could be a V2 feature if needed.
