---
name: openbird-gotchas
description: |
  Known pitfalls, edge cases, and common mistakes when using OpenBird.
  Load alongside other OpenBird skills to avoid common errors.
---

# Gotchas & Pitfalls

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

After creating auth and api, you MUST call `getCsrfToken` and `getUserInfo`:

```javascript
const { xCsrfToken } = await api.getCsrfToken(auth);
const { userId } = await api.getUserInfo(auth, xCsrfToken);
```

Without this, API calls will fail silently or return auth errors.

### Cookies expire

Feishu cookies have a limited lifetime. If API calls start failing with HTTP 401 or empty responses, the cookie has expired. Get a fresh one from the Feishu web client.

---

## IDs

### chatId vs encryptedChatId

Most methods use `chatId` (19-digit numeric string like `'7599271773103737795'`). But `markChatRead()` uses `encryptedChatId` (a base64-like string like `'XISwqtc1G8VCEtA6iEwynw'`). Don't mix them up.

### userId vs userHashId

Most methods use `userId` (string). But `markSession()`, `unmarkSession()`, and `getUserRelation()` use `userHashId` (a number, e.g. `2079738859`). These are different representations of the same user.

---

## Messages

### sendMessage returns raw buffer, not parsed JSON

`sendMessage`, `sendFileMessage`, `sendImageMessage`, `sendMentionMessage`, and `sendReply` return `{ data: Buffer, status: number }`, not `{ success, data }`. The buffer is a raw protobuf response.

Most other methods return `{ success: boolean, data?, error? }`.

### Must upload before sending files/images

You can't send a file/image directly. You must:
1. `uploadImage()` or `uploadFile()` first to get a key
2. Then `sendImageMessage(key)` or `sendFileMessage(key)`

### @mention displayName must match text

In `sendMentionMessage()`, the `displayName` in the mentions array must exactly match the `@displayName` pattern in the text:

```javascript
// WRONG: text says @Alice but mention says Bob
api.sendMentionMessage(auth, 'Hi @Alice', chatId, [
  { userId: '123', displayName: 'Bob' }
]);

// CORRECT: text and displayName match
api.sendMentionMessage(auth, 'Hi @Alice', chatId, [
  { userId: '123', displayName: 'Alice' }
]);
```

---

## Scheduled Messages

### scheduleTime is in milliseconds

Use `Date.now() + offset` or `date.getTime()`. If you pass seconds, the SDK auto-detects and converts, but prefer milliseconds to be explicit.

### patchType numbers

- `1` = UPDATE (change time/content, or send immediately)
- `2` = SUSPEND
- `3` = DELETE

---

## Events

### Always ACK webhook immediately

Respond HTTP 200 before processing the event. OpenBird retries with exponential backoff (500ms -> 8s, 5 attempts) if it doesn't get a 200.

### Deduplicate by event_id

The same event may be delivered more than once. Always check `event.event_id` before processing.

### Skip own messages

When building a reply bot, filter out messages from your own `userId` to avoid infinite loops. Also filter `sender.type === 'bot'`.

---

## API Domains

OpenBird uses multiple Feishu domains internally. You don't need to know these, but for debugging:

| Domain | Used for |
|--------|----------|
| `internal-api-lark-api.feishu.cn` | Main protobuf gateway (all 40+ API methods) |
| `internal-api-lark-file.feishu.cn` | File/image upload |
| `s1-imfile.feishucdn.com` | Image download (CDN) |
| `open.feishu.cn` | Webhook bot management (JSON) |
| `internal-api.feishu.cn` | Bot search |
| `msg-frontier.feishu.cn` | WebSocket connection |
