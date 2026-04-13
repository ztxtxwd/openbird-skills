# Chat & Session Management API

## Create P2P Chat

```javascript
const result = await api.createChat(auth, userId)
// result: { chatId: '7599271773103737795' }
```

Creates a direct message conversation with a user. Returns the `chatId`.

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

## Update Group Chat

```javascript
await api.patchGroupChat(auth, chatId, { name?, description?, iconKey? })
```

At least one field must be provided.

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

## Pin / Unpin Session

```javascript
await api.pinSession(auth, chatId)
await api.unpinSession(auth, chatId)
```

These correspond to current MCP tools `pin_session` and `unpin_session`.

## List Chat Pins

```javascript
const result = await api.pullChatPins(auth, chatId)
```

Use this to inspect current pinned items / pinned messages for a chat when supported by your current OpenBird version.

## Mark / Unmark Session

```javascript
await api.markSession(auth, userHashId)
await api.unmarkSession(auth, userHashId)
```

| Param | Type | Description |
|-------|------|-------------|
| `userHashId` | number | User hash ID (numeric, not string) |

**Note**: These take `userHashId`, not `chatId`.
