---
name: openbird-gotchas
description: |
  Known pitfalls, edge cases, and common mistakes when using OpenBird.
  Load alongside other OpenBird skills to avoid common errors.
---

# Gotchas & Pitfalls

## Modes

### OpenBird now uses explicit modes

Older docs used `OPENBIRD_WEBHOOK_URL` to implicitly decide whether webhook forwarding was enabled. Current OpenBird uses explicit commands instead:

```bash
npx openbird mcp
npx openbird relay http://localhost:3000/webhook
```

Relay mode does not start the MCP server, and MCP mode does not forward webhook events.

---

## Auth

### FeishuAuth constructor takes NO arguments

```javascript
// WRONG
const auth = new FeishuAuth(cookieStr);

// CORRECT
const auth = new FeishuAuth();
auth.prepareAuth(cookieStr);
```

### Must bootstrap before using API

After creating auth and api, you must call `getCsrfToken` and `getUserInfo`:

```javascript
const { xCsrfToken } = await api.getCsrfToken(auth);
const { userId } = await api.getUserInfo(auth, xCsrfToken);
```

### Cookies expire

If API calls start failing with HTTP 401 or empty responses, the cookie has likely expired.

---

## IDs

### chatId vs encryptedChatId

Most methods use `chatId`. But `markChatRead()` uses `encryptedChatId`. Don't mix them up.

### userId vs userHashId

Most methods use `userId`. But `markSession()`, `unmarkSession()`, and `getUserRelation()` use `userHashId` (numeric).

---

## Messages

### sendMessage returns raw buffer, not parsed JSON

`sendMessage`, `sendFileMessage`, `sendImageMessage`, `sendMentionMessage`, and `sendReply` return `{ data: Buffer, status: number }`, not `{ success, data }`.

### Must upload before sending files/images

Upload first, then send using the returned key.

### @mention displayName must match text

In `sendMentionMessage()`, the mention display name must exactly match the `@displayName` text fragment.

### scheduleTime is in milliseconds

Scheduled message APIs use milliseconds.

### `download_file` writes to a local temp path

Current MCP `download_file` saves the attachment under `/tmp/openbird-downloads` and returns metadata including an absolute local `path`.

### History/thread MCP tools can materialize local media

`get_chat_history` and `get_thread_messages` accept `download_media`. When enabled, OpenBird may add `localImages`, `localFiles`, and local `image_path` content parts to the result.

---

## Calendar

### Calendar times are in seconds, not milliseconds

Calendar APIs like `createCalendarEvent`, `updateCalendarEvent`, `webListCalendarEvents`, and `getBusyUser` use Unix seconds.

### eventKey vs eventId

Calendar events have both:
- `calendarRsvp()`, `updateCalendarEvent()`, `deleteCalendarEvent()` use **eventId**
- `createMeetingMinute()`, `transferCalendarEvent()`, `webShareCalendarEvent()` use **eventKey**

### transferCalendarEvent uses userHashId, not userId

`targetUserHash` is numeric.

---

## Documents

### DocxEditor requires open() before any operation

Call `editor.open()` before insert/edit/delete/move operations.

### Wiki URLs need resolveWikiToken first

For `/wiki/...`, resolve the wiki token first to get `objToken`.

For `/docx/...`, the URL token is already the `objToken`.

### MCP document tools only support docx wiki objects

Current MCP document helpers accept `/wiki/` and `/docx/` URLs, but wiki objects must resolve to object type `22`.

---

## Events

### Message webhook payloads use `data.chat`, not `data.conversation`

Current normalized message events place chat metadata under `data.chat`.

### Large webhook IDs may be strings

OpenBird normalizes BigInt-like values before JSON delivery, so some large numeric fields may appear as strings.

### Always ACK webhook immediately

Respond HTTP 200 before processing. OpenBird retries with exponential backoff if it does not receive a 2xx response.

### Deduplicate by event_id

The same event may be delivered more than once.

### Skip own messages and bot messages

When building reply bots, filter out your own user ID and `sender.type === 'bot'`.

### Only `im.thread.reply_count_v1` currently carries `semantic`

Do not assume every event type includes a top-level `semantic` object.

---

## API Domains

OpenBird uses multiple Feishu domains internally. For debugging:

| Domain | Used for |
|--------|----------|
| `internal-api-lark-api.feishu.cn` | Main protobuf gateway |
| `internal-api-lark-file.feishu.cn` | File/image upload |
| `s1-imfile.feishucdn.com` | Image download |
| `open.feishu.cn` | Webhook bot management |
| `internal-api.feishu.cn` | Bot search |
| `msg-frontier.feishu.cn` | WebSocket connection |
