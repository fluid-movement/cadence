# Workflows (FSM)

Cadence uses configurable finite state machines (FSMs) to define how tasks move through statuses. Different task types follow different workflows.

## Concepts

- **Workflow** — a named FSM template. Global, not per-project. Projects select which workflows they use.
- **Task type** — every task has a type (e.g. Bug, Feature, Design Task). The type determines which workflow applies.
- **Status** — a node in the FSM. Represents where a task currently is.
- **Transition** — a directed edge between statuses. Defines what movement is allowed and under what conditions.

## Workflow Configuration

Workflows are global templates managed by users with the `can_configure_fsm` permission (see [roles-permissions.md](roles-permissions.md)).

A workflow defines:
- A list of statuses (name, display color, description)
- A list of valid transitions (from status → to status)
- Per-transition rules (see below)

When a project is set up, the admin selects which workflows are available for that project.

> **TODO:** The UX for assigning workflows to projects needs further spec.

## Transition Rules

Each FSM transition can define:

### Mandatory Fields
A list of fields (core or custom) that must be filled before the transition is allowed. For example:
- Transition to "Ready for Deploy" requires `deploy_notes` custom field to be filled
- Transition to "In QA" requires `qa_instructions` custom field to be filled

### Role Restrictions
Which roles are allowed to trigger this transition. For example:
- Only users with `can_close_task` permission can transition to "Done"
- Only team leads can transition a task back to "In Progress" from "Blocked"

### Task Type Conditions
Transitions can behave differently or be restricted based on whether the task is a parent or child:
- `if_parent_task` — rule only applies when task has children
- `if_child_task` — rule only applies when task has a `parent_id`

## Example Workflow: Bug

```
New → In Progress → In Review → Ready for Deploy → Done
                 ↘ Blocked ↗
```

- Transition "In Review → Ready for Deploy" requires: `deploy_notes` filled
- Transition "Ready for Deploy → Done" restricted to: users with deploy permission

## Example Workflow: Design Task

```
Briefing → In Progress → Review → Approved → Done
                       ↘ Revision Required ↗
```

## Status Display

Each status has:
- Name
- Display color (for Kanban and list views)
- Optional description

## Workflow Versioning

> **TODO:** Determine behavior when a workflow is updated and tasks are mid-flight on an old version. Options: migrate in place, freeze old tasks, or allow both versions to coexist.
