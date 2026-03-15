# Calendar, Meeting Rooms & Scheduling API

## Get Calendar Events

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

## Create Calendar Event

```javascript
const result = await api.createCalendarEvent(auth, calendarId, params, options?)
// result: { success, data? }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarId` | string | Target calendar ID |
| `params.summary` | string | Event title (required) |
| `params.startTime` | number | Start time, Unix timestamp in seconds (required) |
| `params.endTime` | number | End time, Unix timestamp in seconds (required) |
| `params.timezone` | string? | Shared timezone for start/end, e.g. `'Asia/Shanghai'` |
| `params.startTimezone` | string? | Override timezone for start |
| `params.endTimezone` | string? | Override timezone for end |
| `params.description` | string? | Plain-text description |
| `params.rrule` | string? | Recurrence rule (RFC 5545 format) |
| `params.isAllDay` | boolean? | Whether the event is all-day |
| `params.isFree` | boolean? | Whether event should not block busy status |
| `params.visibility` | number? | Visibility enum |
| `params.status` | number? | Status enum |
| `params.locations` | Array? | `[{ name, address, type, latitude, longitude }]` |
| `params.reminders` | Array? | `[{ minutes, method }]` |
| `params.attendees` | Array? | Attendee objects |
| `options.notificationType` | number? | Notification type enum |

```javascript
await api.createCalendarEvent(auth, calendarId, {
  summary: 'Team Standup',
  startTime: Math.floor(Date.now() / 1000) + 3600,
  endTime: Math.floor(Date.now() / 1000) + 5400,
  timezone: 'Asia/Shanghai',
  reminders: [{ minutes: 15, method: 1 }],
});
```

## Update Calendar Event

```javascript
const result = await api.updateCalendarEvent(auth, eventId, updates, options?)
// result: { success, data? }
```

| Param | Type | Description |
|-------|------|-------------|
| `eventId` | string | Calendar event ID |
| `updates.summary` | string? | Updated title |
| `updates.description` | string? | Updated description |
| `updates.startTime` | number? | Updated start time (Unix seconds) |
| `updates.endTime` | number? | Updated end time (Unix seconds) |
| `updates.startTimezone` | string? | Start timezone |
| `updates.endTimezone` | string? | End timezone |
| `updates.isAllDay` | boolean? | All-day flag |
| `updates.isFree` | boolean? | Free/busy flag |
| `updates.visibility` | number? | Visibility enum |
| `updates.rrule` | string? | Recurrence rule |
| `updates.locations` | Array? | Updated locations |
| `updates.reminders` | Array? | Updated reminders |
| `updates.attendees` | Array? | Updated attendees |
| `options.span` | number? | Recurring update scope: `0` = NONE_SPAN, `1` = THIS_EVENT |
| `options.instanceStartTime` | number? | Specific recurring instance timestamp |
| `options.notificationType` | number? | Notification type |

```javascript
await api.updateCalendarEvent(auth, eventId, {
  summary: 'Updated Title',
  startTime: Math.floor(Date.now() / 1000) + 7200,
  endTime: Math.floor(Date.now() / 1000) + 9000,
});
```

## Delete Calendar Event

```javascript
const result = await api.deleteCalendarEvent(auth, calendarId, eventId, options?)
// result: { success }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarId` | string | Calendar ID |
| `eventId` | string | Event ID to delete |
| `options.deleteType` | number? | Delete mode (default: `0` = this event only) |
| `options.needNotification` | boolean? | Notify attendees (default: `true`) |

## List Calendar Events by Time Range

```javascript
const result = await api.webListCalendarEvents(auth, calendarId, startTime, endTime, options?)
// result: { success, calendarId, events }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarId` | string | Calendar ID |
| `startTime` | number | Start time, Unix seconds |
| `endTime` | number | End time, Unix seconds |
| `options.limit` | number? | Max events to return (default: 100) |

```javascript
// Get events for the next 7 days
const now = Math.floor(Date.now() / 1000);
const result = await api.webListCalendarEvents(auth, calendarId, now, now + 7 * 86400);
```

## Batch-Fetch Events by IDs

```javascript
const result = await api.webMgetEventsWithIds(auth, eventKeys)
// result: { success, events: { [eventKey]: eventDetail } }
```

| Param | Type | Description |
|-------|------|-------------|
| `eventKeys` | string[] | Event keys to fetch |

## RSVP to Calendar Event

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
```

## Reply to Calendar Event Invitation

```javascript
const result = await api.replyCalendarEventInvitation(auth, calendarEventKey, replyType, options?)
// result: { success, calendarEvent?, rsvpCommentFailed? }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarEventKey` | Object | `{ uniqueKey, calendarId, originalTime }` |
| `replyType` | string | `'accept'`, `'decline'`, `'tentative'`, `'delete'`, `'needs_action'` |
| `options.rsvpCommentInfo` | Object? | `{ comment?, replyCommentId?, inviteOperatorId? }` |
| `options.needsBriefEvents` | boolean? | Request brief event data in response |

---

## Meeting Minutes

### Create Meeting Minute

```javascript
const result = await api.createMeetingMinute(auth, calendarId, eventKey, options?)
// result: { success, meetingMinuteUrl }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarId` | string | Calendar ID |
| `eventKey` | string | Calendar event key (from `CalendarEvent.key`, distinct from event ID) |
| `options.originalTime` | number? | Original event timestamp (default: `0`) |

Returns a Feishu doc URL for the created meeting minute.

---

## Event Transfer & Sharing

### Transfer Calendar Event

```javascript
const result = await api.transferCalendarEvent(auth, calendarId, eventKey, targetUserHash, options?)
// result: { success, events }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarId` | string | Calendar ID |
| `eventKey` | string | Event key |
| `targetUserHash` | number | Target user hash ID (numeric) |
| `options.originalTime` | number? | Original event timestamp (default: `0`) |

### Share Calendar Event

```javascript
const result = await api.webShareCalendarEvent(auth, chatIds, calendarId, eventKey, options?)
// result: { success, sharedFailedChats }
```

| Param | Type | Description |
|-------|------|-------------|
| `chatIds` | number[] | Chat IDs to share to (numeric) |
| `calendarId` | string | Calendar ID |
| `eventKey` | string | Event key |
| `options.originalTime` | number? | Original event timestamp (default: `0`) |

### Upgrade to Video Meeting

```javascript
const result = await api.upgradeToMeeting(auth, eventInfo, options?)
// result: { success, meetingId, meetingChatId, summary }
```

| Param | Type | Description |
|-------|------|-------------|
| `eventInfo.eventId` | string | Event ID |
| `eventInfo.uid` | string | Event unique key |
| `eventInfo.calendarId` | string | Calendar ID |
| `eventInfo.startTime` | number | Start time (Unix seconds) |
| `eventInfo.startTimezone` | string | Start timezone, e.g. `'Asia/Shanghai'` |
| `eventInfo.endTime` | number | End time (Unix seconds) |
| `eventInfo.summary` | string | Event title |
| `options.originalTime` | number? | Original event timestamp (default: `0`) |
| `options.rrule` | string? | Recurrence rule |

---

## Busy Status

### Check User Busy Status

```javascript
const result = await api.getBusyUser(auth, calendarIds, startTime, endTime, options?)
// result: { success, calendarBusyMap }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarIds` | string[] | Calendar IDs to check |
| `startTime` | number | Start time, Unix seconds |
| `endTime` | number | End time, Unix seconds |

---

## Calendar Search

### Search Across Calendars

```javascript
const result = await api.multiCalendarSearch(auth, query, options?)
// result: { success, contents, calendarChatterMap, calendarBriefMap }
```

| Param | Type | Description |
|-------|------|-------------|
| `query` | string | Search keyword |
| `options.offset` | number? | Result offset |
| `options.count` | number? | Result count |

---

## Meeting Rooms & Buildings

### Search Meeting Rooms

```javascript
const result = await api.searchMeetingRooms(auth, startTime, endTime, options?)
// result: { success, buildings, hasMore, cursor }
```

| Param | Type | Description |
|-------|------|-------------|
| `startTime` | number | Start time, Unix timestamp |
| `endTime` | number | End time, Unix timestamp |
| `options.keyword` | string? | Search keyword |
| `options.count` | number? | Max results |
| `options.timezone` | string? | Timezone (e.g. `'Asia/Shanghai'`) |
| `options.minCapacity` | number? | Minimum room capacity |
| `options.needEquipments` | string[]? | Required equipment list |

### List Buildings

```javascript
const result = await api.pullBuildings(auth)
// result: { success, buildings }
```

Returns all buildings that contain meeting rooms.

### List Meeting Rooms in Building

```javascript
const result = await api.pullMeetingRoomsInBuilding(auth, buildingIds, startTime, endTime, options?)
// result: { success, resources, resourceWeights }
```

| Param | Type | Description |
|-------|------|-------------|
| `buildingIds` | string[] | Building IDs to query |
| `startTime` | number | Start time, Unix seconds |
| `endTime` | number | End time, Unix seconds |
| `options.rrule` | string? | Recurrence rule for recurring booking |
| `options.timezone` | string? | IANA timezone |
| `options.minCapacity` | number? | Minimum room capacity |
| `options.needEquipments` | string[]? | Required equipment |

### Search Guests (Calendar Attendees)

```javascript
const result = await api.searchGuests(auth, query, options?)
// result: { success, guests }
```

| Param | Type | Description |
|-------|------|-------------|
| `query` | string | Search keyword |
| `options.offset` | number? | Result offset (default: 0) |
| `options.limit` | number? | Result limit (default: 50) |

### Get Place Tips (Location Autocomplete)

```javascript
const result = await api.getPlaceTips(auth, keyword, options?)
// result: { success, places }
```

| Param | Type | Description |
|-------|------|-------------|
| `keyword` | string | Place search keyword |
| `options.locale` | string? | Locale code (default: `'zh_CN'`) |
