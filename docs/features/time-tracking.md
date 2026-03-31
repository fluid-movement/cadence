# Time Tracking

## Overview

Cadence includes built-in time tracking. Users log time against tasks, and the logged time feeds into version hour reporting and billing exports.

## Timer UI

When a user has a task open, a timer widget is displayed:
- A **play button** starts the timer
- The timer counts up in real time
- Stopping the timer creates a time log entry on the task
- Only one timer can run at a time per user

The timer widget is lightweight — it should not interrupt the task view UX.

## Time Log Entries

Each time log entry records:
- User
- Task
- Start time
- End time (or duration)
- Optional note/description

Users can also manually add time log entries (for cases where they forgot to start the timer).

## User Time View

Each user has a personal time view showing:
- All their logged time entries
- Filterable by date range, project, task
- Total hours per day / week / project

## Version Hour Tracking

On the version planning view, the PM sees:
- **Estimated hours** — sum of `time_estimate` across all tasks in the version
- **Logged hours** — sum of actual time tracked against those tasks
- Breakdown per team

## Export

Time logs are exportable for billing purposes.

> **TODO:** Exact export format and workflow to be defined. Open questions:
> - CSV, PDF, or both?
> - Grouped by project / team / user / task?
> - Does the export feed into an external billing system, or is it a standalone report?
> - Date range selection for export

## Permissions

- Users can view and manage their own time logs
- PMs and team leads can view time logs across their project
- Exporting time data requires a specific permission (configurable via roles)
