# Reactions, Threads, Urgent, Baike & More

## Emoji Reactions

### Add Reaction

```javascript
await api.addEmojiReaction(auth, messageId, emojiType)
```

### Remove Reaction

```javascript
await api.removeEmojiReaction(auth, messageId, emojiType)
```

| Param | Type | Description |
|-------|------|-------------|
| `messageId` | string | Message ID |
| `emojiType` | string | Emoji type: `'THUMBSUP'`, `'SMILE'`, `'HEART'`, etc. |

These correspond to current MCP tools `add_reaction` and `remove_reaction`.

---

## Threads

### Create Thread

```javascript
await api.createThread(auth, messageId)
```

Creates a thread from an existing message. The message becomes the thread root.

---

## Urgent Notifications

### Send Urgent

```javascript
await api.putUrgent(auth, chatId, messageId, targetUserId, senderUserId, options?)
```

| Param | Type | Description |
|-------|------|-------------|
| `chatId` | string | Chat ID |
| `messageId` | string | Message to mark urgent |
| `targetUserId` | string | Who to notify |
| `senderUserId` | string | Current user ID (sender) |
| `options.urgentType` | number? | `1` = APP, `2` = SMS, `3` = PHONE |

### Query Urgent Ack Status

```javascript
await api.pullUrgentAckStatus(auth, chatId, {
  messageId,
  targetUserId,
  urgentAckId?,
  urgentType?
})
```

---

## Baike / Lingo (飞书词典)

### Get Baike Card

```javascript
const result = await api.getBaikeCard(auth, entityId, text, options?)
// result: { success, html?, info?, error? }
```

| Param | Type | Description |
|-------|------|-------------|
| `entityId` | string | Baike entity ID |
| `text` | string | Term text |
| `options.entityType` | number? | Entity type (default: `1` = enterprise) |
| `options.chatId` | string? | Chat context |
| `options.messageId` | string? | Message context |

Returns glossary card details such as term, full name, definition, detail URL, and contributors when available.

### Get Baike Recommended Docs

```javascript
const result = await api.getBaikeRecommendDocs(auth, entityId, options?)
// result: { success, entityId, docs }
```

| Param | Type | Description |
|-------|------|-------------|
| `entityId` | string | Baike entity ID |
| `options.entityType` | number? | Entity type (default: `1`) |

---

## Tenants

### Get Tenant Info by IDs

```javascript
const result = await api.pullTenantsByIds(auth, tenantIds)
// result: { success, data: { tenants } }
```

---

## System / Config

### Get Config

```javascript
const result = await api.getConfig(auth, 'PROFILE_NAME_DISPLAY_TYPE')
```

Current MCP tool: `get_config`.

### Get Feature Flags

```javascript
const result = await api.getFeatureFlags(auth, { syncToken? })
```

Current MCP tool: `get_feature_flags`.
