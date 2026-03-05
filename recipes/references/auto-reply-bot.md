# Recipe: Auto-Reply Bot

A complete example that receives messages via webhook and auto-replies using the OpenBird SDK.

## Architecture

```
Feishu
  | (WebSocket push)
  v
OpenBird (npx openbird)
  | (HTTP POST)
  v
Your webhook server (this code)
  | (calls OpenBird SDK)
  v
Feishu (sends reply)
```

## Project Setup

```bash
mkdir my-feishu-bot && cd my-feishu-bot
pnpm init
pnpm add openbird openbird-webhook-node express
```

## .env

```
OPENBIRD_COOKIE=your_cookie_string_here
OPENBIRD_WEBHOOK_URL=http://localhost:3000/webhook
```

## bot.mjs

```javascript
import express from 'express';
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';

const app = express();
app.use(express.json());

// Initialize OpenBird SDK
const auth = new FeishuAuth();
auth.prepareAuth(process.env.OPENBIRD_COOKIE);
const api = new FeishuApi();

// Bootstrap auth
const { xCsrfToken } = await api.getCsrfToken(auth);
const { userId: myUserId } = await api.getUserInfo(auth, xCsrfToken);
console.log(`Authenticated as ${myUserId}`);

// Track processed events for idempotency
const processed = new Set();

app.post('/webhook', async (req, res) => {
  const event = req.body;
  res.status(200).send('ok'); // Always ACK immediately

  // Deduplicate
  if (processed.has(event.event_id)) return;
  processed.add(event.event_id);

  // Only handle text messages
  if (event.type !== 'im.message.receive_v1') return;

  const { data } = event;

  // Don't reply to own messages
  if (data.sender.id === myUserId) return;

  // Don't reply to bots
  if (data.sender.type === 'bot') return;

  // Send reply
  const chatId = data.conversation.id;
  const text = data.content.text;
  await api.sendMessage(auth, `Echo: ${text}`, chatId);
});

app.listen(3000, () => console.log('Webhook server on :3000'));
```

## Running

Terminal 1 — Start your webhook server:
```bash
node bot.mjs
```

Terminal 2 — Start OpenBird (connects WebSocket + forwards events):
```bash
npx openbird
```

## Key Points

1. **Always ACK immediately** — respond 200 before processing, or OpenBird will retry
2. **Deduplicate by event_id** — same event may arrive more than once
3. **Skip own messages** — compare `data.sender.id` against your `myUserId`
4. **Skip bot messages** — check `data.sender.type !== 'bot'` to avoid loops
