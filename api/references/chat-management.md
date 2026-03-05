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

```javascript
await api.patchGroupChat(auth, '7600445044713098206', { name: 'New Name' });
await api.patchGroupChat(auth, '7600445044713098206', { description: 'Updated' });
```

## Mark Chat as Read

```javascript
await api.markChatRead(auth, encryptedChatId, readPosition)
```

| Param | Type | Description |
|-------|------|-------------|
| `encryptedChatId` | string | Encrypted chat ID (NOT the regular chatId) |
| `readPosition` | number | Read position timestamp |

```javascript
await api.markChatRead(auth, 'XISwqtc1G8VCEtA6iEwynw', 1767067184);
```

**Note**: This uses `encryptedChatId`, not the regular `chatId`. The encrypted form looks like a base64 string (e.g. `'XISwqtc1G8VCEtA6iEwynw'`).

## Pin / Unpin Session

```javascript
await api.pinSession(auth, chatId)    // Stick to top of feed
await api.unpinSession(auth, chatId)  // Remove from top
```

## Mark / Unmark Session

```javascript
await api.markSession(auth, userHashId)    // Flag as important
await api.unmarkSession(auth, userHashId)  // Remove flag
```

| Param | Type | Description |
|-------|------|-------------|
| `userHashId` | number | User hash ID (numeric, not string) |

**Note**: These take `userHashId` (a number), not `chatId`.
