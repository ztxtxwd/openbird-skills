# Reactions, Calendar, Threads & Urgent API

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
| `messageId` | string | Message ID (e.g. `'7604769001905884091'`) |
| `emojiType` | string | Emoji type: `'THUMBSUP'`, `'SMILE'`, `'HEART'`, etc. |

---

## Calendar

### Get Calendar Events

```javascript
const result = await api.getCalendarEvents(auth, userId)
// result: { success, events: [...] }
```

Each event contains:

| Field | Type | Description |
|-------|------|-------------|
| `eventId` | string | UUID |
| `title` | string | Event title |
| `startTime` | number | Unix timestamp (seconds) |
| `endTime` | number | Unix timestamp (seconds) |
| `timezone` | string | e.g. `'Asia/Shanghai'` |
| `attendees` | Array | `[{ name, userId, rsvpStatus, type }]` |
| `rsvpStatus` | number | Current user's RSVP status |

### RSVP to Calendar Event

```javascript
await api.calendarRsvp(auth, eventId, userId, rsvpStatus)
```

| Param | Type | Description |
|-------|------|-------------|
| `eventId` | string | Calendar event ID (UUID) |
| `userId` | string | Current user ID |
| `rsvpStatus` | string or number | `'accept'`/`1`, `'decline'`/`2`, `'tentative'`/`3` |

```javascript
await api.calendarRsvp(auth, eventId, userId, 'accept');
await api.calendarRsvp(auth, eventId, userId, 'decline');
```

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
| `options.urgentType` | number? | `1` = APP (default), `2` = SMS, `3` = PHONE |

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

## System / Config

### Get Config

```javascript
const result = await api.getConfig(auth, 'PROFILE_NAME_DISPLAY_TYPE')
```

### Get Feature Flags

```javascript
const result = await api.getFeatureFlags(auth, { syncToken? })
```
