# Conductor

- `roster`: `list_models`.
- `spawn`: `create_workspace({projectId, name, agent:"claude"|"codex"|"cursor", model, effort})`, then `send_message`.
- `status`: `get_session_status` (`idle` / `working` / `error`).
- Read results with `list_messages?after=`.
- `cancel`: `cancel_session`.
- **No push notifications.** Use `schedule` (`conductor routine`) to wake every 10–15 min and reconcile in one pass. Never use a tight loop.
- The API documents cloud workspaces. Confirm local repo access before you rely on it.

## Single-vendor harnesses (degraded)
