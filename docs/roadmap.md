# Roadmap

## V1 — Core System

The minimum viable product covering the team's daily workflow.

### User & Access Management
- User accounts with optional team and user group assignments
- Role-based permission system (deny by default)
- Super user role (assigned to the first created user)
- Customer accounts (managed via role configuration)

### Projects & Versions
- Project creation and management
- Version planning: assign tasks to versions, track estimated vs. used hours
- Project status view for PMs

### Tasks
- Full task anatomy: project, team, author, assignee, spectators, priority, due date, time estimate, description, attachments, comments, custom fields
- Sub-tasks (parent-child via `parent_id`, one level deep)
- Task templates (default description/flow templates per project)
- Comment visibility (default: all; can be restricted to user groups)

### Workflows
- Configurable FSM workflows (global templates, assigned to projects)
- Custom task types with their own status flows
- Mandatory fields per FSM transition
- FSM conditions: "is parent task" / "is child task"

### Custom Fields
- Configurable field definitions per workflow/task type
- Fields can be marked mandatory at specific FSM transitions

### Query Language
- URL-param based filtering powering all list views
- Saved personal filters
- See [query-language.md](features/query-language.md) — **spec TODO**

### Time Tracking
- Live timer UI on tasks (play/pause)
- Time log viewable per user
- Export for billing (format TBD)

### Notifications
- Decoupled event system with pluggable delivery handlers
- Core events: assigned, commented, status changed, mentioned

### Reporting (V1)
- Saved query-based task list views
- Simple project dashboard: version progress, hours assigned vs. used

---

## V2 — Extended Features

Features planned after V1 is stable.

### Reporting (V2)
- Kanban board view
- Gantt diagram
- Extended project/team reporting

### AI & Automation
- FSM automation actions (webhooks, LLM prompts, GitLab actions, field updates)
- Cadence as an MCP server (tasks, projects, workflows exposed as MCP resources/tools)
- Embedded AI chat UI in the browser (MCP client with full task context)
- AI coding agent integration — two options documented, decision deferred:
  - Option A: trigger an existing coding agent (e.g. Claude Code) via API
  - Option B: custom agent built on the Claude API
- Full agentic dev loop: spec → LLM validates → agent implements → dev reviews MR

### GitLab Integration
- Self-hosted GitLab integration
- Link commits to tasks via task number in commit message
- Task view shows linked commits and history

### REST API
- Full public REST API exposing all application functionality

### CLI / TUI Tool
- Separate project (Go)
- Terminal UI consuming the REST API
- Designed for developer use on local machines

---

## Deferred / TODO

These areas need further spec sessions before implementation:

- Query language syntax and design
- Workflow-per-project assignment UX
- Time tracking export format and billing workflow
- Notification handler implementations (email, Slack, etc.)
