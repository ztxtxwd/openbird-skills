# Chat History & Search API

## Get Chat Meta

```javascript
const result = await api.getChatMeta(auth, chatId)
// result: { success, chat: { lastMessageId, lastMessagePosition, readPosition } }
```

Returns metadata about a chat — useful to know where to start fetching history. Current MCP tools in this area are `get_chat_meta`, `get_chat_history`, and `search_in_chat`.

## Get Chat History

```javascript
const result = await api.getChatHistory(auth, chatId, options?)
// result: { success, messages: [...] }
```

| Param | Type | Description |
|-------|------|-------------|
| `chatId` | string | Chat ID |
| `options.positions` | number[]? | Specific position numbers to fetch |
| `options.startPosition` | number? | Start position for range query |
| `options.count` | number? | Number of messages (default: 20) |

Messages are ordered by position (sequence number). Higher = newer.

If no `startPosition` is given, it auto-fetches from the latest message.

Each message object contains:

| Field | Type | Description |
|-------|------|-------------|
| `position` | number | Sequence number |
| `messageId` | string | Message ID |
| `type` | number | `2` = POST/rich, `4` = TEXT |
| `typeName` | string | Human-readable type |
| `fromId` | string | Sender user ID |
| `fromType` | number | `1` = USER, `2` = BOT |
| `createTime` | number | Unix timestamp |
| `text` | string | Plain text content |
| `htmlText` | string | Rich text HTML (for POST type) |
| `imageKeys` | string[] | Extracted image keys |
| `parentMsgId` | string | Parent message ID (for replies) |

```javascript
// Get latest 20 messages
const result = await api.getChatHistory(auth, chatId);

// Get 50 messages starting from position 500
const result = await api.getChatHistory(auth, chatId, {
  startPosition: 500,
  count: 50
});
```

**Note**: Fetching history also auto-caches image encryption info for any images found (via cmd=77). This allows subsequent `downloadImage()` calls to work for encrypted images.

## Global Search

```javascript
const result = await api.searchSome(auth, query)
// result: { data: [...userAndGroupIds] }
```

Searches for users and groups by keyword. Returns matched IDs.

## Search Within a Chat

```javascript
const result = await api.searchInChat(auth, query, chatId, options?)
// result: { success, results }
```

| Param | Type | Description |
|-------|------|-------------|
| `query` | string | Search keyword |
| `chatId` | string | Chat ID to search within |
| `options.pageSize` | number? | Results per page (default: 5) |

```javascript
const result = await api.searchInChat(auth, 'meeting notes', '7599271773103737795');
```
