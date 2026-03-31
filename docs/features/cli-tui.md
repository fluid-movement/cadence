# CLI / TUI Tool

> **STATUS: V2 — separate project, depends on REST API**

## Overview

A terminal-based interface for Cadence, built in Go, consuming the REST API. Designed for developers who prefer to stay in the terminal.

## Goals

- Quick task lookup and status updates without opening a browser
- Log time against tasks from the terminal
- Browse assigned tasks, update statuses, post comments
- Fast and lightweight — runs on any machine with network access to the Cadence instance

## Technology

- **Language:** Go
- **UI framework:** TBD (likely [Bubble Tea](https://github.com/charmbracelet/bubbletea) or similar Go TUI library)
- **Auth:** API token stored in local config file

## Planned Features

- List and filter tasks (using the query language via API)
- View task details
- Update task status (trigger FSM transitions)
- Assign tasks, update fields
- Quick time logging (start/stop timer, manual entry)
- Post comments

## Relationship to Main Project

- This is a **separate project/repository** from the main Cadence application
- It depends entirely on the V2 REST API
- No shared code with the main application

## Open Questions

- TUI library choice (Bubble Tea vs. alternatives)
- Local config format for storing API URL and token
- Should it support multiple Cadence instances (profiles)?
