# GitLab Integration

> **STATUS: V2 — not in scope for initial release**

## Overview

Deep integration with a self-hosted GitLab instance. The goal is to link development activity (commits, merge requests, branches) directly to Cadence tasks.

## Convention

A common convention of including the task number in every commit message is the foundation of the integration. This is the foundation of the integration.

Example commit: `PROJ-42: fix null pointer in user service`

## Planned Features

### Commit Linking
- Cadence parses GitLab webhooks for commit messages containing task IDs
- Linked commits appear in the task's activity/history section
- Shows: commit hash, author, message, timestamp, branch, link to GitLab

### Task View — Git Activity Section
- A dedicated section on the task showing all linked commits and merge requests
- Read-only view pulling live data from GitLab

### Merge Request Linking
- MRs referencing a task ID are automatically linked
- Task shows MR status (open, merged, closed)

### Status Automation (Optional)
- Optionally trigger FSM transitions based on GitLab events
  - e.g. MR merged → automatically move task to "Ready for Deploy"
- Configurable per workflow

## Setup

- GitLab webhook configured to send events to Cadence
- Cadence stores a GitLab API token for fetching additional context
- Configuration done by super user in system settings

## Open Questions

- Which GitLab events to handle (push, MR, pipeline, tag?)
- Should branch naming also be part of the convention?
- Read-only linking only, or also write back to GitLab (e.g. post comments on MR)?
