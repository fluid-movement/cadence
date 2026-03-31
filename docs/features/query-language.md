# Query Language

> **STATUS: TODO — needs a dedicated spec session before implementation**

## Intent

The query language is the backbone of all list views, saved filters, and dashboards in Cadence. Rather than hardcoding business logic into specific screens, every task list is a query.

## Goals

- Power all task list views with the same underlying system
- Allow PMs and team leads to build and save their own views
- Expose filters via URL params so views can be bookmarked and shared
- No hardcoded "magic" screens — the dashboard is just a well-crafted query

## Expected Capabilities

At minimum, the query language should support filtering by:
- Project, team, version
- Assignee, author (including `currentUser()` shorthand)
- Status, task type
- Priority, due date (including relative dates like "overdue", "due this week")
- Labels, user groups
- Custom field values
- Parent/child task status

## Open Questions

- **Syntax:** SQL-like text (e.g. `project = "acme" AND assignee = currentUser()`) vs. structured filter chips in the UI that generate URL params behind the scenes vs. both?
- **User-facing complexity:** How technical are end users? JQL is powerful but has a learning curve for non-developers.
- **Sorting and grouping:** Should the query language also define sort order and group-by, or is that separate?
- **Saved filter scoping:** Saved filters are personal (V1). Shared/team filters could be V2.

## Related

- Saved filters are personal (see [reporting.md](reporting.md))
- URL params are the persistence format — a saved filter is essentially a saved URL
