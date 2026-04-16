# Event Type Payload Structures

## im.message.receive_v1 (New Message)

The most important event type. Fired when a new message is received in any chat.

```json
{
  "type": "im.message.receive_v1",
  "event_id": "evt_7604769001905884091",
  "timestamp": 1739347200000,
  "data": {
    "id": "7604769001905884091",
    "chat": {
      "id": "7599271773103737795",
      "type": "group"
    },
    "sender": {
      "id": "7128839302827827201",
      "type": "user"
    },
    "content": {
      "type": "text",
      "text": "hello"
    },
    "thread_id": null
  }
}
```

Current normalized webhook payloads use `data.chat`, not `data.conversation`.

### Chat types

| Value | Meaning |
|-------|---------|
| `p2p` | Direct message |
| `group` | Group chat |
| `topic_group` | Topic group |

### Sender types

| Value | Meaning |
|-------|---------|
| `user` | Human user |
| `bot` | Bot/app |

### Content types

| Value | Meaning |
|-------|---------|
| `text` | Plain text |
| `rich` | Rich text / post |
| `image` | Image |
| `file` | File |
| `audio` | Audio |
| `system` | System message |
| `sticker` | Sticker |
| `media` | Video/media |
| `card` | Interactive card |
| `calendar` | Calendar |
| `share_group_chat` | Shared group |
| `merge_forward` | Merged forward |
| `share_user_card` | Shared user card |
| `todo` | Todo item |

## im.message.reaction_v1 (Emoji Reaction)

Fired when someone adds or removes an emoji reaction.

```json
{
  "type": "im.message.reaction_v1",
  "event_id": "evt_...",
  "timestamp": 1739347200000,
  "data": { ... }
}
```

## im.message.read_count_v1 (Read Count)

Message read count changed.

## im.chat.read_state_v1 (Chat Read State)

Chat-level read position updated.

## im.thread.reply_count_v1 (Thread Reply Count)

Thread reply count changed.

Current OpenBird behavior has one notable special case: `im.thread.reply_count_v1` may include a top-level `semantic` object that describes routing / dedupe semantics for the signal.

```json
{
  "type": "im.thread.reply_count_v1",
  "event_id": "evt_123",
  "timestamp": 1712023530000,
  "semantic": {
    "entity": "thread",
    "action": "activity_detected",
    "kind": "state_signal"
  },
  "data": {
    "chatId": "chat_1",
    "threadId": "thread_1",
    "replyCount": 12,
    "updateTime": "1712023530000"
  }
}
```

Do **not** assume other event types also include `semantic` — in current OpenBird, this is the exception, not the rule.

Large numeric fields may be stringified in webhook JSON because OpenBird normalizes BigInts before delivery.

## im.message.urgent_v1 (Urgent)

Someone sent an urgent notification.

## im.message.urgent_ack_v1 (Urgent Acknowledged)

Urgent notification was acknowledged.

## calendar.event.sync_v1 (Calendar Sync)

Calendar event created/updated/deleted.

## im.chat.top_notice_v1 (Pinned Notice)

Chat pinned notice changed.

## system.device.status_v1 (Device Status)

Device online/offline status changed.

## system.event.unknown (Unknown)

Unrecognized push events. The raw data is passed through:

```json
{
  "type": "system.event.unknown",
  "event_id": null,
  "timestamp": 1739347200000,
  "data": {
    "command": 99999,
    "raw": { ... }
  }
}
```

## Webhook Delivery

Events are delivered as HTTP POST with JSON body. Retry policy:

- **5 attempts** with exponential backoff: 500ms, 1s, 2s, 4s, 8s
- Use `event_id` for idempotency checks (the same event may be delivered more than once)
