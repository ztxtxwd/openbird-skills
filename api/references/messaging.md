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
| `fileKey` | string | File key from `uploadFile()` |
| `chatId` | string | Chat ID |

Upload first with `api.uploadFile()`, then send the returned key.

## Send Image Message

```javascript
await api.sendImageMessage(auth, imageKey, chatId)
```

| Param | Type | Description |
|-------|------|-------------|
| `imageKey` | string | Image key from `uploadImage()` |
| `chatId` | string | Chat ID |

Upload first with `api.uploadImage()`, then send the returned key.

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

Both `rootId` and `parentId` are required. For a direct reply, they are usually the same.

## Forward Message

```javascript
const result = await api.forwardMessage(auth, {
  messageId: '7604769001905884091',
  chatIds: ['7599271773103737795', '7600445044713098206']
})
// result: { success, messages, feedCards }
```

## Message Links

Current OpenBird also exposes message-link capabilities, both as low-level APIs and as MCP tools `put_message_link` and `get_message_link_permission`.

### Create Message Link

```javascript
const result = await api.putMessageLink(auth, fromId, options?)
// result: { success, token?, tokenUrl?, error? }
```

| Param | Type | Description |
|-------|------|-------------|
| `fromId` | string \| number \| bigint | Source entity ID |
| `options.from` | string \| number? | Source type/context |
| `options.copiedIds` | Array? | Message IDs to include in the link |

Use this to generate a Feishu message link for a chat, thread, or message-thread context.

### Check Message Link Permission

```javascript
const result = await api.getMessageLinkPermission(auth, token)
// result: { success, permission?, permissionName?, applinkUrl?, error? }
```

| Param | Type | Description |
|-------|------|-------------|
| `token` | string | Message link token |

## MCP tool names

Current OpenBird MCP tool names in this area are:
- `send_message`
- `send_mention_message`
- `send_reply`
- `put_message_link`
- `get_message_link_permission`

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
await api.putScheduleMessage(auth, 'Reminder!', chatId, Date.now() + 3600000)
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

### Query Scheduled Messages

```javascript
await api.pullScheduleMessageByIds(auth, chatId, ['sm_123', 'sm_456'])
await api.pullChatScheduleMessages(auth, chatId, options?)
```

`pullChatScheduleMessages` options:

| Param | Type | Description |
|-------|------|-------------|
| `options.entityType` | number? | `1` = CHAT_ONLY, `2` = THREAD_ONLY |
| `options.entityId` | string? | Entity ID (defaults to chatId) |
| `options.status` | number[]? | Filter statuses, default `[1, 2]` |
