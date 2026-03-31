# Users, Teams & Groups

## Users

Every person who accesses Cadence has a user account. This includes internal staff, customers, and external contractors.

### User Fields

| Field | Notes |
|---|---|
| Name | Display name |
| Email | Used for login and notifications |
| Team | Optional. One team assignment. |
| User Groups | Optional. Multiple group assignments. |
| Roles | One or more roles determining permissions |
| Notification preferences | Per-event notification settings |

## Teams

Teams represent the functional departments of the agency.

Examples: `dev`, `design`, `online-marketing`, `hr`, `operations`

- A user belongs to **at most one team**
- Team assignment is optional (customers typically have none)
- Tasks are assigned to a team to indicate which department owns the work
- A single project has tasks across multiple teams

Teams are managed by super users and are a simple enumerable list. They are not a permission boundary on their own — permissions are handled by roles.

## User Groups

User groups are a separate, orthogonal concept to teams. They are used for:
- Restricting comment visibility (e.g. only "internal" can see internal comments)
- Potentially filtering task lists or notifications in the future

Examples: `internal`, `frontend`, `backend`, `management`, `client-acme`

- A user can belong to **multiple user groups**
- User group assignment is optional
- Groups are managed by super users

### Team vs. Group — When to Use Which

| Use case | Use |
|---|---|
| "This task is owned by the design team" | Team on the task |
| "Only internal staff should see this comment" | User group on the comment |
| "Show me all dev tasks" | Filter by team |
| "Notify the backend group" | User group on notification |

## Account Creation

- Accounts are created by super users or users with the appropriate permission
- The first account created automatically receives the super user role
- Customer accounts are created and managed like any other account; their access is controlled entirely by role assignment
