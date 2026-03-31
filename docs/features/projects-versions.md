# Projects & Versions

## Projects

A project represents a client engagement or internal initiative. Multiple teams (dev, design, marketing, etc.) work on the same project simultaneously.

### Project Fields

| Field | Notes |
|---|---|
| Name | Display name |
| Description | Optional summary |
| Active workflows | Which FSM workflow templates are available for tasks in this project |
| Members | Users assigned to this project (with roles determining access) |
| Task templates | Default templates available when creating tasks in this project |

### Project Setup

When creating a project, an admin selects:
- Which workflow templates apply (from the global workflow library)
- Which task types are available
- Default task templates

> **TODO:** Detailed UX for project setup and workflow assignment.

## Versions

A version represents a planned batch of tasks targeted at a specific deployment. This is not a time-box (not a sprint) — it's a deploy-driven grouping.

### Version Fields

| Field | Notes |
|---|---|
| Name / number | e.g. "v2.4.1" or "2024-W12" |
| Project | The project this version belongs to |
| Status | Planning / Active / Shipped |
| Tasks | All tasks assigned to this version |
| Total estimated hours | Sum of `time_estimate` across all tasks in the version |
| Total logged hours | Sum of time tracked against tasks in this version |

### Version Workflow

1. PM creates a new version for a project
2. PM pulls tasks from the backlog and assigns them to the version
3. PM and team leads review together, adjust estimates, assign tasks to team members
4. Version becomes "Active" — work begins
5. Tasks move through their FSM statuses
6. When all tasks are done and the deploy goes out, version is marked "Shipped"

### Version Planning View (V1)

The PM has a version planning view showing:
- All tasks in the version, grouped by team or status
- Total estimated hours vs. total logged hours
- Per-team hour breakdown
- Quick task status overview

### Shipped Versions

Shipped versions remain visible as a historical record. The project has a "shipped versions" view (similar to Jira's releases view) showing what was delivered and when.

## Backlog

Tasks not assigned to any version live in the project backlog. The backlog is a filterable task list (powered by the query language) and is the PM's primary workspace for triage and version planning.
