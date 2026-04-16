---
name: openbird-events
description: |
  OpenBird Relay-mode event handling. Load when writing code to receive
  Feishu push events — incoming messages, reactions, read state, urgent
  notifications, calendar sync, or any server-side event forwarded by OpenBird.
---

# Receiving Events

In **Relay mode**, OpenBird receives server push events via WebSocket, normalizes them, and forwards them to your webhook URL as HTTP POST requests.

## How It Works

```
Feishu Server
  | (WebSocket push)
  v
OpenBird Relay
  | (HTTP POST, JSON body)
  v
Your webhook endpoint
```

Run OpenBird in Relay mode:

```bash
OPENBIRD_COOKIE="..." npx openbird relay http://localhost:3000/webhook
```

## Event Format

Every event is a JSON object with this structure:

```json
{
  "type": "im.message.receive_v1",
  "event_id": "evt_7604769001905884091",
  "timestamp": 1739347200000,
  "data": { ... }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Event type identifier |
| `event_id` | string | Unique event ID (for idempotency) |
| `timestamp` | number | Milliseconds since epoch |
| `data` | object | Event payload (varies by type) |

For message events, current normalized payloads use `data.chat`, not `data.conversation`.

Large numeric protocol values may appear as strings in webhook JSON because OpenBird normalizes BigInt-like values to JSON-safe forms before delivery.

## Event Type Quick Reference

| Type | Description |
|------|-------------|
| `im.message.receive_v1` | New chat message |
| `im.message.reaction_v1` | Emoji reaction added/removed |
| `im.message.read_count_v1` | Message read count changed |
| `im.message.urgent_v1` | Urgent notification sent |
| `im.message.urgent_ack_v1` | Urgent acknowledged |
| `im.chat.read_state_v1` | Chat read state updated |
| `im.thread.reply_count_v1` | Thread reply count changed |
| `im.thread.read_state_v1` | Thread read state |
| `im.thread.updated_v1` | Thread metadata updated |
| `im.chat.tabs_v1` | Chat tabs changed |
| `im.chat.top_notice_v1` | Chat pinned notice |
| `im.message.preview_v1` | Message preview |
| `feed.cards_v1` | Feed card updates |
| `calendar.event.sync_v1` | Calendar event sync |
| `system.device.status_v1` | Device online/offline |
| `system.event.unknown` | Unrecognized event (passthrough) |

See `references/event-types.md` for detailed payload structures.
See `references/webhook-node-setup.md` for setting up the webhook receiver.
