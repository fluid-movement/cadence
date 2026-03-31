# AI & Automation

> **STATUS: V2**

## Overview

Cadence aims for deep AI integration — not just surface-level "summarize this task" features, but a full agentic loop where a well-specified task can be implemented with minimal developer intervention. The developer's role shifts toward writing clear specs and reviewing AI output.

---

## Layer 1: FSM Automation Actions

FSM transitions can fire **automation actions** in addition to (or instead of) validating fields. Actions are configured per-transition in the workflow editor.

### Action Types

| Action | Description |
|---|---|
| Post comment | Post a message or LLM response as a task comment |
| Send webhook | HTTP POST to an external URL with task context as payload |
| Send LLM prompt | Send a configured prompt + task context to an LLM; post response as comment |
| GitLab: create issue | Create a linked GitLab issue from task data |
| GitLab: create branch | Create a feature branch named after the task ID |
| GitLab: create MR | Open a merge request linked to the task |
| Trigger coding agent | Invoke an AI coding agent to implement the task (see Layer 3) |
| Update task field | Set a field value automatically on transition |
| Assign task | Auto-assign to a user or role |

Actions are **composable** — a single transition can fire multiple actions in sequence.

### Prompt Configuration

LLM prompt actions are configured with:
- A prompt template (with placeholders for task fields: `{{title}}`, `{{description}}`, `{{assignee}}`, etc.)
- Which task context to include (description only, all fields, comment history, attachments)
- What to do with the response (post as comment, fill a field, trigger a further transition)

---

## Layer 2: GitLab Automation

See also: [gitlab-integration.md](gitlab-integration.md)

FSM actions can interact with GitLab directly:

- **Create issue** — mirrors the Cadence task as a GitLab issue, linked by task ID
- **Create branch** — creates a `feature/PROJ-42-short-description` branch
- **Create MR** — opens a draft MR targeting the default branch, linked to the task

Typical automated flow:
1. Task transitions to "In Progress" →
2. Action: create GitLab issue + create feature branch →
3. Developer (or agent) works on the branch →
4. Branch pushed with MR → task auto-transitions to "In Review"

---

## Layer 3: AI Coding Agent Integration

The most ambitious layer: an LLM actually implements the task. Two architectural options are documented here — a final decision is deferred.

### Option A: Integrate with an Existing Coding Agent

Use an existing coding agent (e.g. Claude Code's headless/API mode) as an external tool. Cadence triggers it via webhook or API call; the agent reads the task spec, writes code, and opens an MR.

**How it works:**
1. FSM transition fires a "trigger coding agent" action
2. Cadence calls the external agent API with task ID and context
3. The agent (running externally) fetches task details — ideally via Cadence's MCP server (see below)
4. Agent implements the task, pushes a branch, opens an MR on GitLab
5. GitLab webhook notifies Cadence → task transitions to "In Review"

**Pros:**
- Leverages best-in-class existing tooling
- No need to build or maintain a coding agent
- Natural fit with the MCP server approach

**Cons:**
- Dependent on external agent API availability and pricing
- Less control over agent behavior
- Requires the MCP server to be built first

### Option B: Build a Custom Coding Agent

Build a lightweight agent using the Claude API (or another LLM API) that is purpose-built for Cadence tasks.

**How it works:**
1. FSM transition triggers the internal agent
2. Agent reads task spec from Cadence
3. Agent uses the Claude API with tool use to: read the codebase, write files, run tests, commit, push, open MR
4. Results are posted back to the task as comments; MR link is attached

**Pros:**
- Full control over agent behavior and prompting
- Can be deeply integrated with Cadence's data model
- No external dependency for the core loop

**Cons:**
- Significant engineering effort
- Maintaining a coding agent is a project unto itself
- Reliability and quality are hard to guarantee

---

## Layer 4: Cadence as an MCP Server

Cadence exposes an **MCP (Model Context Protocol) server** so that any MCP-compatible AI client (Claude Code, Claude Desktop, custom agents) can natively interact with Cadence.

### Exposed MCP Resources

- Tasks (read, create, update, transition)
- Projects and versions
- Users and teams
- Workflows and statuses
- Time logs

### Exposed MCP Tools

- `get_task(id)` — fetch full task details
- `list_tasks(query)` — query tasks using the Cadence query language
- `update_task(id, fields)` — update task fields
- `transition_task(id, transition)` — trigger an FSM transition
- `post_comment(task_id, body, visibility)` — post a comment
- `log_time(task_id, duration, note)` — log time against a task

### Why MCP?

If Cadence is an MCP server, any coding agent (Option A above, or a developer using Claude Code locally) can access full task context without custom integration. The developer's workflow becomes:

1. Open Claude Code (or any MCP client)
2. Agent reads the task spec via MCP
3. Agent implements, pushes, opens MR
4. MR linked back to task via GitLab integration

No glue code needed per-agent — the MCP server is the universal interface.

---

## Embedded AI Chat UI (MCP Client in the Browser)

The Cadence web UI includes an embedded AI chat interface — an MCP client running in the browser. This allows users to interact with an AI that has full context of the current task, project, or view.

### Use Cases

- "Does this task description have enough detail to implement?"
- "Break this task into sub-tasks"
- "Summarize the activity on this task"
- "What tasks are blocked in the current version?"
- "Draft a QA checklist for this feature"

### Implementation

The browser-based MCP client connects to:
1. Cadence's own MCP server (for task/project data)
2. An LLM API (Claude or configurable provider)

The LLM can both read context and take actions (post comments, update fields, trigger transitions) via MCP tools — subject to the user's permissions.

---

## Agentic Dev Loop — Full Vision

The ideal end-to-end flow when all layers are in place:

```
Task created
  → PM writes spec in description
  → LLM action on transition: "Is this spec implementable? What's missing?"
  → PM iterates on spec based on feedback
  → PM sets task to "Ready for Implementation"
  → Automation: create GitLab branch + issue
  → Automation: trigger coding agent
  → Agent reads spec via MCP, implements, opens draft MR
  → Task transitions to "In Review"
  → Developer reviews MR, requests changes or approves
  → MR merged → task transitions to "Done"
```

Developer involvement: spec review + MR review. Everything else is automated.

---

## Open Questions

- Which LLM providers to support? Claude only (Anthropic API), or provider-agnostic?
- Option A vs. Option B for coding agent — decision deferred
- Security model: what can the embedded AI chat do vs. what requires explicit user action?
- MCP server: authentication for external MCP clients (API tokens, same as REST API?)
- Should automation actions be audited/logged on the task for transparency?
