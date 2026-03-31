# Roles & Permissions

Cadence uses a deny-by-default permission system. Nothing is accessible unless a role explicitly grants it.

## Core Principles

- All permissions are **off by default**
- Access is granted via **roles**
- A user can have **multiple roles** (permissions are additive)
- Roles control **view**, **create**, and **edit/delete** on all UI elements and actions
- There are no hardcoded special users — even the super user is just a role

## Super User Role

- A built-in role that grants all permissions
- Automatically assigned to the **first user created** in the system
- Can be assigned to additional users by other super users
- Responsible for: user management, role configuration, workflow configuration, system settings

## Permission Granularity

Permissions cover:
- Viewing/editing tasks (by project, team, or globally)
- Creating/managing projects and versions
- Managing users, roles, and user groups
- Configuring workflows (`can_configure_fsm`)
- Triggering specific FSM transitions
- Viewing/posting restricted comments
- Accessing reporting and dashboards
- Exporting data (time logs, etc.)

The full permission list is defined in application code but is configurable via the admin UI.

## Customer Role (Example)

Customers have direct accounts. A minimal customer role would grant only:
- Create task (in their assigned project)
- View their own tasks
- Post comments (public visibility only)

Everything else is denied. This is achieved purely through role configuration — no special customer handling in the codebase.

## Role Configuration

Roles are managed by super users. Each role has:
- Name
- Description
- A list of granted permissions (each with view/create/edit scope)

## Permission Inheritance

No inheritance between roles. Permissions are strictly additive across all roles assigned to a user.

## `can_configure_fsm`

A specific permission that grants access to create and edit global workflow templates. Separate from general admin access so that a "Workflow Admin" role can exist without full super user access.
