# Messaging API

## Send Text Message

```javascript
await api.sendMessage(auth, text, chatId)
```

| Param | Type | Description |
|-------|------|-------------|
| `auth` | Object | Auth object |
| `text` | string | Message text (supports Markdown) |
| `chatId` | string | Chat ID (19-digit string, e.g. `'7599271773103737795'`) |

Returns `{ data: Buffer, status: number }`.

Markdown is auto-converted. See getting-started for supported syntax.

## Send File Message

```javascript
await api.sendFileMessage(auth, fileKey, chatId)
```

| Param | Type | Description |
|-------|------|-------------|
| `fileKey` | string | File key from `uploadFile()` (e.g. `'file_v3_00ve_...'`) |
| `chatId` | string | Chat ID |

You must upload the file first with `api.uploadFile()`, then send the returned key.

## Send Image Message

```javascript
await api.sendImageMessage(auth, imageKey, chatId)
```

| Param | Type | Description |
|-------|------|-------------|
| `imageKey` | string | Image key from `uploadImage()` (e.g. `'img_v3_02vf_...'`) |
| `chatId` | string | Chat ID |

You must upload the image first with `api.uploadImage()`, then send the returned key.

## Send Message with @Mentions

```javascript
await api.sendMentionMessage(auth, text, chatId, mentions)
```

| Param | Type | Description |
|-------|------|-------------|
| `text` | string | Message text with `@displayName` patterns |
| `chatId` | string | Chat ID |
| `mentions` | Array | `[{ userId: string, displayName: string }]` |

The `@displayName` in the text must match the `displayName` in the mentions array.

```javascript
await api.sendMentionMessage(auth, 'Hey @Alice please review', chatId, [
  { userId: '7441015090323947523', displayName: 'Alice' }
]);
```

## Send Reply

```javascript
await api.sendReply(auth, text, chatId, { rootId, parentId })
```

| Param | Type | Description |
|-------|------|-------------|
| `text` | string | Reply text (supports Markdown) |
| `chatId` | string | Chat ID |
| `rootId` | string | Root message ID of the thread |
| `parentId` | string | Message ID being replied to |

Both `rootId` and `parentId` are required. For a direct reply, they are the same.

---

## Schedule Messages

### Create Scheduled Message

```javascript
await api.putScheduleMessage(auth, text, chatId, scheduleTime, options?)
```

| Param | Type | Description |
|-------|------|-------------|
| `text` | string | Message content (supports Markdown) |
| `chatId` | string | Chat ID |
| `scheduleTime` | number | Unix timestamp in milliseconds |
| `options.entityId` | string? | Thread ID (defaults to chatId) |

Returns `{ success, scheduleMessage? }`.

```javascript
// Schedule 1 hour from now
await api.putScheduleMessage(auth, 'Reminder!', chatId, Date.now() + 3600000);
```

### Update/Delete Scheduled Message

```javascript
await api.patchScheduleMessage(auth, chatId, messageId, patchType, options?)
```

| Param | Type | Description |
|-------|------|-------------|
| `chatId` | string | Chat ID |
| `messageId` | string | Schedule message ID |
| `patchType` | number | `1` = UPDATE, `2` = SUSPEND, `3` = DELETE |
| `options.scheduleTime` | number? | New time in ms (for UPDATE) |
| `options.content` | string? | New content (for UPDATE) |
| `options.sendImmediately` | boolean? | Send now (for UPDATE) |

```javascript
// Delete a scheduled message
await api.patchScheduleMessage(auth, chatId, msgId, 3);

// Send immediately
await api.patchScheduleMessage(auth, chatId, msgId, 1, { sendImmediately: true });

// Reschedule
await api.patchScheduleMessage(auth, chatId, msgId, 1, { scheduleTime: Date.now() + 7200000 });
```

### Query Scheduled Messages

```javascript
// By specific IDs
await api.pullScheduleMessageByIds(auth, chatId, ['sm_123', 'sm_456'])

// All pending/suspended in a chat
await api.pullChatScheduleMessages(auth, chatId, options?)
```

`pullChatScheduleMessages` options:

| Param | Type | Description |
|-------|------|-------------|
| `options.entityType` | number? | `1` = CHAT_ONLY (default), `2` = THREAD_ONLY |
| `options.entityId` | string? | Entity ID (defaults to chatId) |
| `options.status` | number[]? | Filter: `[1]` = PENDING, `[2]` = SUSPEND, default `[1, 2]` |
