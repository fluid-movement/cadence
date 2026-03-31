# Notifications

## Design Philosophy

Notifications are **fully decoupled** from delivery. The application is responsible for:
1. Detecting that a notification-worthy event has occurred
2. Firing a notification event with all relevant context

Delivery handlers (email, Slack, in-app, webhook, etc.) are separate implementations of a common interface. Adding a new delivery channel requires only a new handler — no changes to core application logic.

## Notification Events (V1)

The following events trigger notifications:

| Event | Who is notified |
|---|---|
| Task assigned to user | The assignee |
| Task unassigned | The previously assigned user |
| Comment posted on task | Assignee + all spectators |
| User mentioned in comment | The mentioned user |
| Task status changed | Assignee + all spectators |
| Task added to a version | Assignee |
| Due date approaching | Assignee |

This list is intentionally extensible — adding new events should be straightforward.

## Handler Interface

All delivery handlers implement a common interface. The application calls the interface; it does not know or care which handlers are registered.

A handler receives:
- Event type
- Recipient user(s)
- Task / project context
- A rendered message body (or raw data to render itself)

Registered handlers are configured at the application level (not per-user). Per-user preferences (which events they want) are a separate layer on top.

## User Notification Preferences

Users can configure which events they want to be notified about. Preferences are per-event-type. The UI shows a simple checklist of notification events.

## In-App Notifications

A notification bell/counter in the UI shows unread notifications. Clicking it opens a notification list. This is independent of any delivery handler — it is always on.

## V1 Handlers

- In-app (always on)
- Email (configured at system level — SMTP settings)

## V2 / Future Handlers

- Slack (webhook-based)
- Custom webhook
- Any other handler implementing the interface
