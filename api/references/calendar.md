# Calendar, Meeting Rooms & Scheduling API

Current MCP tool names in this area include `get_calendar_events`, `mget_events_with_ids`, `get_place_tips`, `create_calendar_event`, `calendar_rsvp`, `reply_calendar_event_invitation`, `delete_calendar_event`, `update_calendar_event`, `web_list_calendar_events`, `create_meeting_minute`, `transfer_calendar_event`, `web_share_calendar_event`, `upgrade_to_meeting`, `search_guests`, `search_meeting_rooms`, `pull_buildings`, and `pull_meeting_rooms_in_building`.

## Get Calendar Events

```javascript
const result = await api.getCalendarEvents(auth, userId)
// result: { success, events: [...] }
```

Each event contains fields like `eventId`, `title`, `startTime`, `endTime`, `timezone`, `attendees`, and `rsvpStatus`.

## Create Calendar Event

```javascript
const result = await api.createCalendarEvent(auth, calendarId, params, options?)
// result: { success, data? }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarId` | string | Target calendar ID |
| `params.summary` | string | Event title (required) |
| `params.startTime` | number | Start time, Unix timestamp in seconds |
| `params.endTime` | number | End time, Unix timestamp in seconds |
| `params.timezone` | string? | Shared timezone for start/end |
| `params.startTimezone` | string? | Override timezone for start |
| `params.endTimezone` | string? | Override timezone for end |
| `params.description` | string? | Plain-text description |
| `params.rrule` | string? | Recurrence rule |
| `params.isAllDay` | boolean? | Whether the event is all-day |
| `params.isFree` | boolean? | Whether event should not block busy status |
| `params.visibility` | number? | Visibility enum |
| `params.status` | number? | Status enum |
| `params.locations` | Array? | Location objects |
| `params.reminders` | Array? | Reminder objects |
| `params.attendees` | Array? | Attendee objects |
| `options.notificationType` | number? | Notification type enum |

## Update Calendar Event

```javascript
const result = await api.updateCalendarEvent(auth, eventId, updates, options?)
```

`updates` can include fields like `summary`, `description`, `startTime`, `endTime`, `startTimezone`, `endTimezone`, `rrule`, `locations`, `reminders`, and `attendees`.

## Delete Calendar Event

```javascript
const result = await api.deleteCalendarEvent(auth, calendarId, eventId, options?)
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarId` | string | Calendar ID |
| `eventId` | string | Event ID to delete |
| `options.deleteType` | number? | Delete mode |
| `options.needNotification` | boolean? | Notify attendees |

## List Calendar Events by Time Range

```javascript
const result = await api.webListCalendarEvents(auth, calendarId, startTime, endTime, options?)
// result: { success, calendarId, events }
```

Current MCP tool: `web_list_calendar_events`.

## Batch-Fetch Events by IDs

```javascript
const result = await api.webMgetEventsWithIds(auth, eventKeys)
// result: { success, events: { [eventKey]: eventDetail } }
```

## RSVP to Calendar Event

```javascript
await api.calendarRsvp(auth, eventId, userId, rsvpStatus)
```

| Param | Type | Description |
|-------|------|-------------|
| `eventId` | string | Calendar event ID |
| `userId` | string | Current user ID |
| `rsvpStatus` | string or number | `'accept'`/`1`, `'decline'`/`2`, `'tentative'`/`3` |

## Reply to Calendar Event Invitation

```javascript
const result = await api.replyCalendarEventInvitation(auth, calendarEventKey, replyType, options?)
// result: { success, calendarEvent?, rsvpCommentFailed? }
```

| Param | Type | Description |
|-------|------|-------------|
| `calendarEventKey` | Object | `{ uniqueKey, calendarId, originalTime }` |
| `replyType` | string | `'accept'`, `'decline'`, `'tentative'`, `'delete'`, `'needs_action'` |
| `options.rsvpCommentInfo` | Object? | Optional RSVP comment payload |
| `options.needsBriefEvents` | boolean? | Request brief event data |

Current MCP tool: `reply_calendar_event_invitation`.

---

## Meeting Minutes

### Create Meeting Minute

```javascript
const result = await api.createMeetingMinute(auth, calendarId, eventKey, options?)
// result: { success, meetingMinuteUrl }
```

Returns a Feishu doc URL for the created meeting minute.

Current MCP tool: `create_meeting_minute`.

---

## Event Transfer & Sharing

### Transfer Calendar Event

```javascript
const result = await api.transferCalendarEvent(auth, calendarId, eventKey, targetUserHash, options?)
```

Uses `targetUserHash` (numeric hash ID), not `userId`.

Current MCP tool: `transfer_calendar_event`.

### Share Calendar Event

```javascript
const result = await api.webShareCalendarEvent(auth, chatIds, calendarId, eventKey, options?)
```

Current MCP tool: `web_share_calendar_event`.

### Upgrade to Video Meeting

```javascript
const result = await api.upgradeToMeeting(auth, eventInfo, options?)
// result: { success, meetingId, meetingChatId, summary }
```

Current MCP tool: `upgrade_to_meeting`.

---

## Busy Status

### Check User Busy Status

```javascript
const result = await api.getBusyUser(auth, calendarIds, startTime, endTime, options?)
// result: { success, calendarBusyMap }
```

---

## Calendar Search

### Search Across Calendars

```javascript
const result = await api.multiCalendarSearch(auth, query, options?)
// result: { success, contents, calendarChatterMap, chatterMap, calendarBriefMap }
```

Use this to search across meeting rooms, shared calendars, and primary calendars.

---

## Meeting Rooms & Buildings

### Search Meeting Rooms

```javascript
const result = await api.searchMeetingRooms(auth, startTime, endTime, options?)
// result: { success, buildings, hasMore, cursor }
```

Current MCP tool: `search_meeting_rooms`.

### List Buildings

```javascript
const result = await api.pullBuildings(auth, options?)
// result: { success, buildings }
```

Current MCP tool: `pull_buildings`.

### List Meeting Rooms in Building

```javascript
const result = await api.pullMeetingRoomsInBuilding(auth, buildingIds, startTime, endTime, options?)
// result: { success, resources, resourceWeights }
```

Current MCP tool: `pull_meeting_rooms_in_building`.

### Search Guests (Calendar Attendees)

```javascript
const result = await api.searchGuests(auth, query, options?)
// result: { success, guests }
```

Current MCP tool: `search_guests`.

### Get Place Tips (Location Autocomplete)

```javascript
const result = await api.getPlaceTips(auth, keyword, options?)
// result: { success, places }
```

Useful when building calendar events with location autocomplete.
