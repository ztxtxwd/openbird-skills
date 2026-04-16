# Chat History & Search API

## Get Chat Meta

```javascript
const result = await api.getChatMeta(auth, chatId)
// result: { success, chat: { lastMessageId, lastMessagePosition, readPosition } }
```

Returns metadata about a chat — useful to know where to start fetching history. Current MCP tools in this area are `get_chat_meta`, `get_chat_history`, `get_thread_messages`, and `search_in_chat`.

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
| `options.count` | number? | Number of messages (default: 20 for MCP, larger defaults may exist in low-level APIs) |

Messages are ordered by position (sequence number). Higher = newer.

If no `startPosition` is given, it auto-fetches from the latest message.

Each message object may contain fields like:

| Field | Type | Description |
|-------|------|-------------|
| `position` | number | Sequence number |
| `messageId` | string | Message ID |
| `type` | number | Raw message type |
| `typeName` | string | Human-readable type |
| `fromId` | string | Sender user ID |
| `fromType` | number | `1` = USER, `2` = BOT |
| `createTime` | number | Unix timestamp |
| `text` | string | Plain-text content |
| `markdown` | string | Markdown/plain rendering used by current APIs |
| `content` | array | Structured message parts such as `{ type: 'text', text }` and `{ type: 'image_key', image_key: { key } }` |
| `imageKey` / `imageKeys` | string / string[] | Extracted image keys |
| `fileKey` / `fileKeys` | string / string[] | Extracted file keys |
| `fileName` | string | Attachment filename when available |
| `fileMime` | string | Attachment MIME type when available |
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

### MCP: `get_chat_history`

Current MCP tool:

- `get_chat_history`

Inputs:

| Param | Type | Description |
|-------|------|-------------|
| `chat_id` | string | Chat ID |
| `start_position` | number? | Cursor position |
| `count` | number? | Number of messages |
| `download_media` | boolean? | When `true`, download referenced image/file attachments and include local temp paths |

When `download_media` is enabled, current MCP behavior can enrich each message with:

- `localImages`: downloaded image metadata with local paths
- `localFiles`: downloaded file metadata with local paths
- `content` image parts remapped from `image_key` to local `image_path` parts when the image was materialized successfully
- top-level `glossaryTerms`: deduplicated Baike/Lingo annotations collected from the fetched messages

**Note**: Fetching history also auto-caches image encryption info for any images found (via cmd=77). This allows subsequent `downloadImage()` calls to work for encrypted images.

## Get Thread Messages

```javascript
const result = await api.getThreadMessages(auth, threadId, options?)
// result: { success, messages: [...], hasMore?, nextPosition? }
```

This fetches the reply chain inside an existing thread/topic.

Current MCP tool:

- `get_thread_messages`

Inputs mirror chat history closely:

| Param | Type | Description |
|-------|------|-------------|
| `thread_id` | string | Thread ID |
| `start_position` | number? | Cursor position |
| `count` | number? | Number of messages |
| `download_media` | boolean? | Download images/files and include local temp paths |

Thread message objects use the same enriched shape described above for chat history, including structured `content`, optional `localImages` / `localFiles`, and glossary aggregation.

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
