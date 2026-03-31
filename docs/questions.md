# Open Questions & Spec Gaps

This document collects all unresolved questions, spec holes, and feature comparisons with other tools. Each item should be explicitly decided before implementation of the relevant area begins.

Mark items as resolved by replacing `[ ]` with `[x]` and adding the decision below the question.

---

## Tasks

### T1 — Task ID Format
The spec says the ID is "used in commit messages and URLs" but doesn't define the format. Options:
- **A: Project-prefixed sequential** — `PROJ-42`, `ACME-7` (Jira style). Human-readable, great for commit messages and verbal communication. Requires a project key/slug.
- **B: Global sequential integer** — `#42`, `#1337`. Simple, but loses project context in commit messages.
- **C: UUID** — `a3f2...`. Globally unique but unreadable in commit messages.

Which format? And if A, does the project have a short key (e.g. "ACME" for the "Acme Corp Website" project)?
the id used in commit messages and urls is in the form "PROJ-42", so each project has a long name, and a unique shorthand. so Urls will be of the form "task/PROJ-42". This will not actually be the ID field in the database of course, as the ID needs to never change. Tasks may get moved to different projects, so the PROJ-42 identifier may change to ACME-45. the number will be an incrementing integer for each project. If a task is moved, calling the old url will just redirect to the new task
---

### T2 — Labels / Tags
Labels (free-form tags) are not in the spec at all. Jira, Linear, GitHub Issues, and Asana all have them. They're useful for cross-cutting concerns that don't fit into team or task type — e.g. `performance`, `tech-debt`, `customer-reported`, `quick-win`.

Do we want a free-form label/tag system on tasks? This would also need to be queryable in the query language.
yes sounds good. lets add a tagging system. a task can have many tags. we will get a searchable dropdown with tags that already exist, but i can also create a new one directly. tags are always lowercased, so we dont get "Todos" and "todos" as separate tags
---

### T3 — Task Dependencies ("Blocks / Is Blocked By")
Not in the spec. Jira has linked issues (`blocks`, `is blocked by`, `relates to`, `duplicates`). Linear has dependencies. This is also required for Gantt charts to show critical paths.

Do we want task linking/dependencies? If yes:
- **A: Simple blocking** — just "blocks/is blocked by" relationships
- **B: Full link types** — configurable link types (blocks, relates to, duplicates, etc.)
- **C: No dependencies** — skip this entirely; versions and ordering are enough

This affects the Gantt diagram spec significantly.
yes we want task dependencies, like in jira. lets get the full link types. we can also expand our FSM to have workflows like "if task that is blocked by another task is in a certain status for 7 days, send an email". we can think about how fancy we want to get with the fsm
---

### T4 — Multiple Assignees
Currently the spec has a single `assignee`. Some tools (Linear, Height) allow multiple assignees. In an agency context, a task might need a developer AND a designer.

Do we want multiple assignees, or is the single assignee + spectators model sufficient? If multiple, how do we handle "who is primarily responsible"?
single assignee
---

### T5 — Story Points vs. Time Estimate
We have `time_estimate` (a duration, e.g. 4h). Jira supports both time estimates and story points (relative sizing: 1, 2, 3, 5, 8, 13 Fibonacci). Some teams prefer story points for planning, then time estimates for billing.

Do we want both, or just `time_estimate`? Story points could be implemented as a custom field, but they have special behavior (velocity calculations, planning poker).
we only need time estimates, no story points. would story points be difficult to add later?
---

### T6 — Start Date
We have `due_date` but not `start_date`. Asana, Monday.com, and others track both. A start date is required for proper Gantt rendering (tasks with duration, not just deadlines).

Do we add `start_date` as a core field?
lets add start date as an optional field
---

### T7 — Activity / Audit Log on Tasks
Not in the spec. Jira shows a full history of every change to a task: "Status changed from In Progress to Done by John at 14:32", "Priority changed from High to Critical by Jane". This is valuable for accountability and debugging.

Do we want an immutable activity log on each task? This is separate from comments — it's auto-generated, not user-written.
yes we want this. we need full accountability. all changes in a task should be documented
---

### T8 — Task Ordering in Backlog
How are tasks ordered in the backlog? Options:
- **A: Manual drag-and-drop rank** — PM manually sorts the backlog by drag-and-drop (Jira, Linear default)
- **B: Sort by field** — sorted by priority, due date, creation date, etc. (configurable)
- **C: Both** — a manual rank field that can be used as a sort key, plus other sort options

Linear has a strong opinion here (manual rank is the default). Jira also defaults to manual rank. This affects how the PM communicates priority implicitly.
what exactly is the rank? I dont understand exactly. We use the jira "priority" field to decide the order of task implementation in a sprint
---

### T9 — Recurring Tasks
Asana and Monday.com support recurring tasks (daily standups, weekly reports, monthly billing tasks). Not in the spec.

Do we need recurring tasks, or is this out of scope? If yes, this is a non-trivial feature.
ne we dont need it. for dev meetings we have a long runnign task that we just book time to. this is fine for now
---

### T10 — Clone / Duplicate Task
Not in the spec. Most tools (Jira, Linear, Asana) allow cloning a task to create a copy with the same fields. Useful for repetitive task creation.

Do we want a "Clone task" action?
No, we want a "task template" function. so when creating a task we can choose a premade template, like "main task with 4 subtasks". But it should be possible to "create template from task". so then instead of starting from a blank template, the template ui would have the selected task prefilled
---

### T11 — Bulk Actions
Not in the spec. In Jira, you can select multiple tasks in a list view and perform bulk operations: change status, reassign, move to version, change priority, add label.

Do we want bulk actions on task lists? This is a significant UX feature, especially for PM backlog triage.
yes we need bulk actions. would this need to be in v1, or can we push it to v2?
---

### T12 — @Mentions in Task Description
We have @mentions in comments triggering notifications. What about @mentions in the task description itself? Does editing the description and @mentioning someone notify them?
@mentions are only important in comments, no @mention in description or other fields
---

### T13 — Task Resolution Field
Jira has a "Resolution" field that's set when closing a task: `Fixed`, `Won't Fix`, `Duplicate`, `Cannot Reproduce`, `By Design`. This is separate from status and persists when a task is reopened.

Do we want a resolution field, or is this complexity we don't need? Could be a custom field instead.
we dont need it, the task history should be in the comments
---

### T14 — Archiving vs. Deleting Tasks
What happens when a task needs to go away?
- Can tasks be deleted? By whom? What happens to linked time logs?
- Should there be a "Won't Do" or "Archived" terminal status instead of hard deletion?
- What happens to sub-tasks when a parent is deleted or archived?
tasks can be deleted. if a parent is deleted, the children are also deleted.
---

### T15 — Task Watchers vs. Spectators
Currently the spec calls them "spectators" — users added to a task who receive notifications. In most tools (GitHub, Linear, Asana), this is called "watching" and is opt-in (you click "Watch" yourself). Our model seems to be that spectators are added by someone else.

- Can a user add themselves as a spectator (self-subscribe)?
- Can a user remove themselves from spectators?
- Is there a difference between "someone added me" and "I subscribed myself"?
any user can manually subscribe. if you book time on a task, write a comment or are mentioned, you are automatically subscribed
---

## Workflows (FSM)

### W1 — Workflow Versioning (In-Flight Tasks)
Already flagged as TODO. When a workflow is updated, what happens to tasks currently in that workflow? Options:
- **A: Migrate in place** — tasks move to the closest equivalent status in the new workflow
- **B: Freeze** — tasks stay on the old workflow version until they reach a terminal status
- **C: Coexistence** — both old and new workflow versions exist; tasks show which version they're on
- **D: Lock** — prevent workflow edits if any tasks are actively using it

This is a hard problem. What's the acceptable tradeoff?
there should be some intelligent migration. so when i edit a workflow that is in use, there should be some wizard that can walk me through it. But we need to decide what the workflows will do exactly. on the one hand we have something like task status: tasks in this project have this FSM for the status. so task status open can have in progress or closed, etc. on the other hand if we are using workflows as automation, like "if task which is blocked for 1 week, send a message to the author". We need to define what the scope of the workflows is first.
---

### W2 — Workflow Diagram Editor
When configuring a workflow, what does the editor look like?
- **A: Visual node graph** — drag-and-drop statuses and draw transitions between them (like draw.io)
- **B: Form-based** — a structured form: define statuses, then define transitions as a table
- **C: Both** — visual overview + form details

A visual editor is more intuitive but significantly more complex to build.
probably something in between, forms that have a visual representation, clicking on nodes shows options for it. no connections via drag and drop, but creating a connection via form should show it in the node graph
---

### W3 — Initial / Default Status
When a task is created, it's placed in the first status automatically. Is the "initial status" always the first one defined in the workflow, or is it explicitly configured per workflow?
following idea, but open for discussion: a task already has an assigned project on creation. why not make a workflow assignment mandatory for projects, that describes the task FSM?
---

### W4 — Terminal Statuses
Are statuses like "Done" or "Closed" semantically special (e.g. they stop the timer, count toward version completion), or are they just regular statuses with no outgoing transitions?
regular statuses, we can then use the query language to build filters to show only open, etc
---

### W5 — Time in Status Tracking
Linear and some Jira plugins track how long a task has been in each status. This is useful for identifying bottlenecks (tasks stuck in "In Review" for days).

Do we want to track time-in-status? This would feed into V2 reporting.
yes lets do that
---

### W6 — Sub-task Workflow
Currently sub-tasks use the same workflow as the parent. But a sub-task might need a simpler flow (e.g. just "To Do / In Progress / Done"). Two options:
- **A: Sub-tasks inherit parent's workflow** (current spec)
- **B: Sub-tasks can have their own workflow** (or a simplified one)
this should be handled in the FSM configuration. we should not decide this in the code, but instead leave that open to the configuration of the workflow. that gives maximum flexibility
---

### W7 — Global vs. Per-Project Workflow Customization
Currently workflows are global templates. But what if project A needs a slightly different version of the "Bug" workflow? Options:
- **A: Global only** — one Bug workflow for everyone, edit it globally (current spec)
- **B: Per-project override** — projects can customize a copy of the global template
- **C: Fully per-project** — each project creates its own workflows from scratch

Linear uses global workflows. Jira allows per-project workflows. A and B seem like the right tradeoff here, but worth confirming.
As i wrote before, lets have per project workflows for task status. also lets talk about what else workflows can automate. maybe then we need to split workflows into "types", so there will be a "task status" type. Or a workflow will target a specific field, so then the workflow would be of type "task status". Lets jam on this
---

## Roles & Permissions

### R1 — Project-Level Roles
Currently roles are global. In Jira, a user can have different roles per project (Developer on project A, Viewer on project B). This is important if customers should only see their own project.

Do we need project-scoped roles, or is it sufficient to control task visibility through project membership + a single global role?
What do you think? i am open to discussion. should users only see projects they are assigned to? then maybe the role could also auto assign or remove assignments? theres another world here
---

### R2 — Permission Scoping: "Own Tasks Only"
A common pattern: a user can edit tasks assigned to them, but not tasks assigned to others. This is more granular than "can edit tasks globally".

Do we need "own tasks" scoping in the permission model? (e.g. `edit_task:own` vs. `edit_task:all`)
i think users can edit any task, if they are allowed to edit tasks at all. we can control which tasks they see and are able to view with the user groups already
---

### R3 — SSO / External Authentication
The spec doesn't mention login method. Options:
- **A: Username/password only**
- **B: Google OAuth / OIDC** — one-click login via Google Workspace (very common in agencies)
- **C: SAML** — enterprise SSO
- **D: LDAP/Active Directory**

For an agency on Google Workspace, option B would be highly convenient. Worth deciding early as it affects the auth implementation.
yes we are using google workspace, so OAuth would be convenient. what would this mean for user management? we would still have users in our own DB, but the authentication would be pushed to google, right? then we can pretty much hand off auth. that would be the best. But if we abstract the "authenticated" status, we can always replace or expand this
---

### R4 — Two-Factor Authentication
Not in the spec. Is 2FA a requirement for any user accounts, especially super users?
this can be part of the "abstraction", right? lets create the abstraction and just see "is the user authenticated", and then we can implement and change the authentcation some other time
---

### R5 — User Deactivation
When an employee leaves, their account shouldn't be deleted (tasks, time logs, history would lose attribution). But they also shouldn't be able to log in.

Do we have a "deactivate" state for users? What happens to their active tasks and running timers?
lets add a deactivated flag to users, these users cant do anything. we can use this in the UI too, grey them out, etc
---

### R6 — Audit Log for Admin Actions
Who changed which role? Who created which user? When was the workflow modified? For security and accountability, admin actions should be logged.

Do we want a system-level audit log for configuration changes?
yes definitely
---

## Users, Teams & Groups

### U1 — User Timezone
Not in the spec. Timezones affect due date display, notification timing ("due date approaching" triggers), and time log display.

Do users have a timezone setting?
yes sure. we are mostly in the same timezone, but lets use best practices regarding timestamps
---

### U2 — Avatar / Profile Picture
Not in the spec. Avatars are a basic UX element in Jira, Linear, Asana — they appear on task assignees, comments, etc. Gravatar auto-resolution or uploaded photo?
Lets jave a gravatar as default, but users can upload a photo. more important for PM with customer facing accounts
---

### U3 — User Capacity / Availability
Some tools (Jira with Tempo, Float, Forecast) track user capacity: "John is available 32h this week, has 28h of estimated work assigned." Useful for planning.

Do we want capacity tracking? This would be a significant addition to the planning view.
I dont think so, because task planning and assignment are done by different people in our company
---

### U4 — Multiple Team Membership
Currently a user belongs to at most one team. But what about developers who split time between frontend dev and design? Or a PM who manages multiple teams?

Is one team per user always sufficient, or do we need multiple?
One team is enough, the teams arent really important right now anyway. user groups are more important for workflow triggers, etc. Are we even using Team Assignment for anything?
---

### U5 — User Invitation Flow
How does a new user join? Options:
- **A: Admin creates account, shares credentials** — simple but insecure
- **B: Admin sends email invite, user sets password** — standard flow
- **C: SSO-only** — no password, login via Google/SAML (depends on R3)
Option B
---

### U6 — Out of Office / Availability Status
Linear and some tools let users mark themselves as away/OOO. This can affect task assignment suggestions and planning.

Do we want any concept of user availability status?
No dont need it
---

## Projects & Versions

### P1 — Project Status / Lifecycle
Can a project be anything other than "active"? What about:
- **Archived** — project is done, no new tasks, still readable
- **On Hold** — temporarily paused
- **Pitch / Pre-sales** — project exists but hasn't started

Do we need project statuses?
No dont need it currently
---

### P2 — Version Dates
Currently a version has a name and status, but no dates. The PM needs to communicate a target release date. A version should probably have:
- `target_date` — when it's planned to ship
- `shipped_date` — when it actually shipped (auto-set when marked Shipped?)

Is this needed for V1?
yes we need this. lets call them "releases". a release has a set date, PM will assign tasks to be done for a release
---

### P3 — Multiple Active Versions
Can a project have multiple active versions simultaneously? Or is there always exactly one "current" version?

Example: you might be deploying v2.4 while already planning v2.5.
the project itself doesnt have a "version", the release is just a separate entity. a project will have a list of releases, some of them already in the past, and some in the future. Our PM will use the ones in the future to plan. maybe we can also add a "hours per release", or something, that PMs can see in the release info. so how many projected man hours are currently projected in the upcoming release. they sell a certain amount, this way they know if they can add more tasks, or should move some to the next release
---

### P4 — Task Assigned to Multiple Versions
Can a task be in more than one version? (e.g. it was planned for v2.3, pushed to v2.4.) Or does re-assigning to a new version remove it from the previous one?
should be single select, i guess we can use the task history for reporting if needed?
---

### P5 — Project Templates
Jira has project templates (e.g. "Software project", "Service desk"). Could Cadence have project templates that pre-configure workflows, task types, and task templates?
sure! lets have that
---

### P6 — Epics
Not in the spec. Jira has Epics as a level above Stories/Tasks — a large body of work spanning multiple versions. Linear has similar concepts.

In the current model, is a "version" the equivalent of an Epic, or do we need a separate concept? Options:
- **A: No epics** — versions + task hierarchy is enough
- **B: Epics as a task type** — an Epic is just a task of type "Epic" that other tasks link to
- **C: Epics as a first-class concept** — a separate entity above tasks
no epics, we are not doing agile
---

### P7 — Project Membership Model
Currently "members" are listed on a project. Questions:
- Is project membership explicit (admin adds you) or implicit (you can see all projects if your role allows)?
- Can you be a member of a project but not see all its tasks (e.g. only tasks assigned to your team)?
- How does a customer know which project is "theirs"?
Im not sure we even need project membership. the project should have an assigned PM, and substitute, maybe also a list of customers, but devs, designers, etc dont really need to be assigned to a project, right? they just get a task assigned
---

### P8 — Version Release Notes
Jira can auto-generate release notes from a version (list of all completed tasks). Could be useful for client communication.

Do we want auto-generated release notes per version? Could be as simple as a rendered list of shipped tasks with descriptions.
there should be a ui for viewing releases, with the list of tasks assigned
---

### P9 — Cross-Project Task Linking
Can a task in Project A link to or depend on a task in Project B? Relevant for agencies where work across projects may share dependencies.
No, a task is always assigned to a single project. otherwise we open a can of worms
---

## Query Language

### Q1 — Visual Builder vs. Text Syntax
Two interaction models, not mutually exclusive:
- **A: Text query language** — JQL-like syntax, typed by the user. Powerful for technical users.
- **B: Visual filter builder** — dropdowns and chips, no typing required. Accessible to non-technical PMs.
- **C: Both** — visual builder generates a query string; advanced users can edit the text directly (Linear does this well).

Which approach for V1?
lets go with C
---

### Q2 — Query Language Scope
Beyond filtering task lists, should the query language also handle:
- Sorting (e.g. `ORDER BY due_date ASC`)
- Grouping (e.g. `GROUP BY assignee`)
- Aggregation for dashboards (e.g. `COUNT tasks WHERE status = "Done"`)

Or is it purely a filter/search language, with sorting/grouping as separate UI controls?
if we are already building this, lets use it to build dashboards and widgets too. lets create a proper spec to enable simple queries
---

### Q3 — Saved Filter Sharing (V2 Scope Confirmation)
Saved filters are personal in V1. Confirming: shared/team filters (a PM sets up a view and shares it with the team) is explicitly V2?
no filters should fit in a url, so should inherently be shareable without any extras. so if i save a filter i have a private url, but there is a "share" button that gives me the filter in written out form. we need this anyway for creating filters on the fly, they would just be get requests with query params
---

### Q4 — Default Views
Should there be system-provided default saved views that all users see? E.g.:
- "My open tasks"
- "All tasks in current version"
- "Unassigned tasks in my team"

Or do users build their own from scratch?
the super user should be able to build global filters that all users can see. so you have a "global" section, and a "my filters" section 
---

## Time Tracking

### TT1 — Billable vs. Non-Billable Time
Not in the spec. Critical for an agency doing client billing. A time log entry should probably be flaggable as billable or non-billable. Some time might be internal (e.g. team meetings, internal tools work) vs. client-billable.

Do we want a `billable: boolean` on time log entries? And can this be defaulted per project (client projects are billable, internal projects are not)?
no, if i as a dev am tracking time i can decide if time is billable, i am just tracking time. what happens with it is the work of the PM or front office who do the billing
---

### TT2 — Time Budget per Project / Version
Not in the spec. A project might have a contracted budget of e.g. 200 hours. Exceeding it is a problem.

Do we want a time budget concept on projects or versions, with a warning when approaching the limit?
maybe, lets move this to v2
---

### TT3 — Time Entry Editing Window
Can users edit their own time log entries freely (even months later)? Or is there a lockout policy (e.g. entries older than 7 days are locked)? This matters for billing integrity.
yes, i think in jira time is editable? 
---

### TT4 — Time Approval Workflow
Some agencies require manager approval of logged time before it can be invoiced. Do we need a time approval workflow?
invoicing is a separate flow that we are implementing in v2, lets not decide that yet
---

### TT5 — Export Format
Already flagged as TODO. Deciding this is important for V1:
- **CSV** — simple, works with Excel/Google Sheets
- **PDF** — for sending to clients directly
- **Integration** — push to an external billing tool (Harvest, Toggl, Lexoffice, etc.)

What billing system does the agency currently use, and does the export need to feed into it?
no idea, i need to check this. lets just create the tracking, and the UI with which users can see their tracked time, like in Jira
---

### TT6 — Time Rounding
Some billing workflows round time to the nearest 15 or 30 minutes. Is this needed, and if so, is it per-user, per-project, or system-wide?
dont need to decide this now. so no rounding for now
---

### TT7 — Manager View of Team Time
Currently "PMs and team leads can view time logs across their project." What does this view look like? Is it just a filterable list, or do they need a summarized view (e.g. per-person hours this week)?
maybe both? we can jam on this.
---

## Notifications

### N1 — Notification Digest / Batching
Getting one email per event is noisy. Options:
- **A: Immediate** — one email per event (current implicit spec)
- **B: Digest** — configurable digest (hourly / daily / weekly summary)
- **C: Both** — user chooses per-event-type whether they want immediate or digest
lets go with immediate, users can always opt out
---

### N2 — Author Notifications
When someone comments on a task I created (but I'm not the assignee), should I be notified? Currently only assignee + spectators are notified on comments.

Should the task author also receive notifications by default?
yes, task author should always be notified. But lets do the following: users that should be notified for changes are taken from a "spectating" list. so if i author a task, i am automatically added to this list. this way i can unsubscribe from a task, but stay as author. clean separation
---

### N3 — Project-Level Subscriptions
"Notify me of all new tasks in project X" — subscribing to a whole project rather than individual tasks. Useful for PMs who want to see everything happening in their project without being on every task.
yes sounds good. should also have options for which actions should trigger a notification
---

### N4 — Notification Grouping
If 20 things happen on the same task in 5 minutes, should notifications be grouped ("5 changes to PROJ-42") rather than 20 separate notifications?
yes, i think there should be some grouping. so notifications will probably be queued, on new changes they would be merged. so there would be for example a 5 minute grace period, then the queue entry is worked on. im open to suggestions though
---

### N5 — Mentions in Task Description
If someone @mentions a user in the task description (not a comment), does that trigger a notification? And if the description is edited and a new @mention is added, does the newly mentioned user get notified?
i think @mentions should only be available in comments
---

### N6 — "Due Date Approaching" — How Early?
The spec lists "due date approaching" as a notification event but doesn't define "approaching". 24h? 48h? User-configurable?
these kind of events should also be "workflows", so no hardcoded logic. the more flexible we make workflows, the less we actually need to hard code
---

## Reporting

### RE1 — Burndown Charts
Classic project management view: a line chart showing remaining work over time for a version (tasks or hours). Not in the spec.

Do we want burndown charts in V1 or V2?
i am not a project manager, so no idea if this is used by ours. lets move it to v2
---

### RE2 — Team Velocity
How many tasks / hours does each team (or user) complete per version? Useful for capacity planning over time.

V1 or V2?
not sure if we need this, so lets move it to v2
---

### RE3 — SLA Tracking
For customer-submitted tasks, are there SLAs? E.g. "respond within 4h, resolve within 3 business days." Jira Service Management tracks this heavily.

Do we need SLA tracking? This could be implemented as a custom workflow feature (transitions must happen within X time), but it needs first-class support to be useful.
again, if our workflows configuration is good, we can do "do x for a task created by someone from group y"
---

### RE4 — Data Export (Tasks)
Beyond time log exports — can users export task lists to CSV/Excel for external reporting? E.g. "export all tasks in version 2.4 to CSV."
no, no csv exporting for now
---

### RE5 — Dashboard Widgets / Personal Dashboard
Does every user have a personal dashboard they can customize? E.g. "my assigned tasks", "version progress", "recent activity"?

Or is the query language + saved filters sufficient as a personal home screen?
we will have dashboards with widgets created from filters, and others, but maybe not in v1. lets move that to v2
---

## AI & Automation

### AI1 — LLM Provider
Should Cadence be tied to Claude/Anthropic, or support any LLM via a configurable provider interface (similar to the notification handler model)?

Pluggable is more work but avoids vendor lock-in.
Definitely pluggable, also it should be opt in. so if nothing is configured the whole app should still work, just without the ai features
---

### AI2 — Automation Action Audit Log
When an FSM automation action fires (LLM prompt sent, GitLab branch created, webhook called), should this be logged on the task activity log? Important for transparency and debugging.
yes definitely logged. the logging should also be pluggable, so we could write to a database, or send data to a service. as always, decouple that!
---

### AI3 — Failed Automation Handling
What happens if an automation action fails? (LLM API is down, GitLab is unreachable, webhook returns 500.)
- Silently fail?
- Post an error comment on the task?
- Block the FSM transition?
- Retry with backoff?
depends on the error, and what the implications would be. lets mark this as todo, we can decide while we are implementing
---

### AI4 — Coding Agent: Option A vs. Option B
Decision deferred. Both options are documented in `ai-automation.md`. When will this decision be made, and what information is needed to make it?
i would move the whole AI feature to V2, so we dont need to decide this yet. we will see how this works when we flesh out the workflows feature
---

## Missing Features — Comparison with Other Tools

These are features present in Jira, Linear, Asana, Monday.com, or Height that are not currently in the spec. Each should be explicitly included or excluded.

### M1 — Global Search
A search bar that searches across all tasks, projects, and comments by keyword. Not the query language — just "find anything containing 'payment gateway'."

Linear, Jira, Asana all have this as a primary navigation feature. Do we want global full-text search?
yes definitely. as we are using postgres, i definitely plan on using pg-vector, to vectorize at least the task deescriptions. then we can also do semantic searching of tasks. very important, as there are often similar problems, or you cant remember exact wording
---

### M2 — Jira Migration / Import
The agency is migrating from Jira. Is there a data import path? Jira supports XML/CSV export.

Do we want a one-time Jira import tool? This could save significant manual re-entry work.
yes we definitely need a migration path, but we can put this off. This will probably be a separate thing, as it will be a "one time" affair
---

### M3 — Email-to-Task
Not in the spec. A project has a dedicated email address; emails sent to it create tasks automatically. Common for customer support workflows and for agencies receiving client requests by email.

Do we want email-to-task creation?
no we dont need that
---

### M4 — Customer Portal vs. Unified UI
Currently customers access the same UI as internal staff, just with restricted permissions. Some tools (Jira Service Management, Zendesk) have a dedicated customer-facing portal with a simplified UX.

Is the unified UI + role restriction approach sufficient, or do customers need a purpose-built simpler view?
unified ui with role restriction is fine
---

### M5 — Keyboard Shortcuts
Not in the spec. Linear is famous for its keyboard-first UX (press `C` to create task, `T` for task list, etc.). GitHub Issues also has shortcuts.

Do we want keyboard shortcuts as a V1 feature for power users?
yes, the whole thing should be keyboard driven. i definitely see a "cmd+k" command palette, from which pages can be reached, actions can be triggered (create new task in project x). the app should be fast and easy to navigate
---

### M6 — Dark Mode
Not in the spec. Expected by most developers. Do we want dark mode support in V1 or V2?
dark mode definitely v1, in v2 i want more themes, users can select a theme in their user account. 
---

### M7 — Mobile / Responsive UI
Not in the spec. Is the application desktop-first only, or does it need to work on mobile browsers?
desktop first, we can see if the layout works on mobile, but not a priority
---

### M8 — Kanban WIP Limits
V2 includes a Kanban board. Kanban methodology includes WIP (Work In Progress) limits — a maximum number of tasks allowed in a status column at once. Do we want WIP limits on the Kanban board?

---

### M9 — Checklist / Sub-items Within a Task
Not in the spec. Beyond formal sub-tasks, many tools (Notion, Linear, GitHub) have lightweight checklists inside the task description or as a separate field. E.g. an acceptance criteria checklist.

Do we want a checklist feature within tasks (separate from sub-tasks)?

---

### M10 — Task Watchers Auto-Add Rules
In some tools, you can configure "automatically add all members of the dev team as spectators on any task of type Bug." Currently spectators are manually added.

Do we want auto-subscription rules?

---

### M11 — Slack Integration (V1 or V2?)
We have Slack listed as a V2 notification handler. But Slack is a primary communication tool for agencies. Should it move to V1?

---

### M12 — Task Changelog / Version History
Related to T7 (activity log). Specifically: can you see what the description looked like 3 days ago? Can you diff versions of the description?

Some tools (Notion, Confluence, Linear) have full description version history.

---

### M13 — Webhooks as a First-Class Feature (Outgoing)
Currently outgoing webhooks appear only in the notifications section and AI automation. Should there be a dedicated "Webhooks" configuration section in the admin UI, separate from notifications, for sending task events to external systems (Zapier, n8n, custom integrations)?

---

### M14 — Two-Factor Authentication
Not in the spec. Required for any accounts with sensitive access (super users, PMs with access to client data). Is 2FA a V1 requirement?

---

### M15 — Full Data Export / Backup
System-level export of all data (tasks, users, time logs, comments, attachments) as a backup or for migration purposes. Not per-user export — full system dump.

Do we need this for self-hosted instances?

---

### M16 — Task Splitting
Splitting a task in Jira creates a new task with the same fields and a link between them. Different from cloning. Useful when a task turns out to be larger than expected and needs to be split mid-sprint.

Do we want a "split task" action?

---

### M17 — Planning Poker / Estimation Sessions
Some tools (Jira, PlanITpoker) support collaborative estimation sessions where the team votes on story points simultaneously. Not in spec.

Is this in scope at all, or is the PM + team lead estimation conversation handled offline?

---

### M18 — Time Zone Handling for Due Dates
Is a due date a calendar date (no time, no timezone) or a specific datetime? E.g. "due by end of day Friday" vs. "due at 14:00 UTC on Friday." This affects notification triggers and Gantt display.

---

### M19 — Spent Time Visibility on Task
Currently time tracking shows the total logged hours on the version view. Should the task itself show how many hours have been logged against it (vs. the estimate)?

---

### M20 — Comments as Structured Threads vs. Linear Conversation
Are comments a flat linear list (oldest first, like Jira) or threaded (can reply to a specific comment, like GitHub/Slack)? Threaded comments are more useful for long discussions but more complex.

---
