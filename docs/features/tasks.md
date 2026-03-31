# Tasks

A task is the fundamental unit of work in Cadence.

## Core Fields

| Field | Type | Notes |
|---|---|---|
| `id` | auto | Unique identifier, used in commit messages and URLs |
| `project` | relation | The project this task belongs to |
| `team` | relation | The team responsible (design, dev, marketing, etc.) |
| `author` | relation (user) | Who created the task |
| `assignee` | relation (user) | Who is responsible for doing the work |
| `spectators` | relation (users) | Users who receive notifications but are not assignees |
| `parent_id` | relation (task) | Set if this is a sub-task; null for root tasks |
| `version` | relation | The version this task is targeted at |
| `status` | FSM state | Determined by the assigned workflow |
| `task_type` | relation | Determines which workflow applies |
| `priority` | configurable list | e.g. P1/P2/P3 or High/Medium/Low — configurable per instance |
| `due_date` | date | Optional |
| `time_estimate` | duration | Planned effort |
| `description` | rich text | Main task body |
| `attachments` | files | File uploads |

## Custom Fields

Custom fields are defined at the workflow/task-type level. They behave like any other field — they can be made mandatory at specific FSM transitions. Examples of fields implemented this way:

- "Deploy notes for DevOps" — required before entering the deploy status
- "QA test instructions" — required before entering the QA review status

See [workflows.md](workflows.md) for how mandatory fields are configured per transition.

## Sub-tasks

- A sub-task is a normal task with `parent_id` set.
- Nesting is **one level only** — sub-tasks cannot have sub-tasks.
- Sub-tasks follow the same FSM as any other task of their type.
- FSM configuration can optionally branch behavior on "is parent task" / "is child task".
- The parent task shows a list of all its children inline.
- Sub-tasks can be created by the PM, team lead, or developer — no restriction.

## Comments

- All users with task visibility can comment by default.
- Comments have a **visibility** setting:
  - Default: visible to all users with access to the task
  - Restricted: visible only to specified user groups (e.g. "internal only")
- Comment authors set visibility when posting.

## Task Templates

- Templates define a default description structure and optionally a default task type/workflow.
- Templates are configured per project.
- When creating a task, the user can select a template to pre-fill the description.

## Priority

Priority values are configurable (not hardcoded). The instance admin defines the priority list. Examples:
- P1 / P2 / P3 / P4
- Critical / High / Medium / Low
- MoSCoW (Must / Should / Could / Won't)

## Task Lifecycle

1. Task is created (by PM, developer, or customer)
2. Task sits in the backlog until assigned to a version
3. PM and team lead estimate time and assign to a developer
4. Developer works the task through the workflow statuses
5. Task is completed and the version is eventually shipped
