# Chat & Session Management API

## Create P2P Chat

```javascript
const result = await api.createChat(auth, userId)
// result: { chatId: '7599271773103737795' }
```

Creates a direct message conversation with a user. Returns the `chatId`.

Current MCP tool: `create_chat`.

## Create Group Chat

```javascript
const result = await api.createGroup(auth, {
  name: 'My Group',
  description: 'Group description',
  memberIds: ['user1', 'user2'],
  chatMode: 1,
  iconKey: 'img_xxx'
})
// result: { success, chatId }
```

| Param | Type | Description |
|-------|------|-------------|
| `name` | string | Group name (required) |
| `description` | string? | Group description |
| `memberIds` | string[]? | User IDs to add |
| `chatMode` | number? | `1` = DEFAULT, `2` = THREAD, `3` = THREAD_V2 |
| `iconKey` | string? | Group icon image key |

Current MCP tool: `create_group`.

## Update Group Chat

```javascript
await api.patchGroupChat(auth, chatId, { name?, description?, iconKey? })
```

At least one field must be provided.

Current MCP tool: `patch_group_chat`.

## Chat Metadata

```javascript
const result = await api.getChatMeta(auth, chatId)
// result: { success, chat: { lastMessageId, lastMessagePosition, readPosition, ... } }
```

Use this when you need chat-level cursors or want a reliable starting point before fetching history. Current MCP tool: `get_chat_meta`.

## Mark Chat as Read

```javascript
await api.markChatRead(auth, encryptedChatId, readPosition)
```

| Param | Type | Description |
|-------|------|-------------|
| `encryptedChatId` | string | Encrypted chat ID (NOT the regular chatId) |
| `readPosition` | number | Read position timestamp |

**Note**: This uses `encryptedChatId`, not the regular `chatId`.

Current MCP tool: `mark_chat_read`.

## Pin / Unpin Session

```javascript
await api.pinSession(auth, chatId)
await api.unpinSession(auth, chatId)
```

These correspond to current MCP tools `pin_session` and `unpin_session`.

## Pin / List / Unpin Messages

```javascript
await api.pinMessage(auth, chatId, messageId)
const pins = await api.pullChatPins(auth, chatId, { cursor?, count?, withMessages? })
await api.unpinMessage(auth, chatId, pinId)
```

Use this to manage message-level pinned items inside a chat.

Current MCP tools:

- `pin_message`
- `list_chat_pins`
- `unpin_message`

`list_chat_pins` supports optional pagination and `with_messages` to include pinned message payloads when available.

## List Chat Pins

```javascript
const result = await api.pullChatPins(auth, chatId)
```

Returns chat pin metadata and can include message payloads when supported by your current OpenBird version.

## Mark / Unmark Session

```javascript
await api.markSession(auth, userHashId)
await api.unmarkSession(auth, userHashId)
```

| Param | Type | Description |
|-------|------|-------------|
| `userHashId` | number | User hash ID (numeric, not string) |

**Note**: These take `userHashId`, not `chatId`.

Current MCP tools: `mark_session` and `unmark_session`.
