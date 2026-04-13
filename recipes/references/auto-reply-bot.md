# Recipe: Auto-Reply Bot

A complete example that receives messages via OpenBird Relay and auto-replies using the OpenBird SDK.

## Architecture

```
Feishu
  | (WebSocket push)
  v
OpenBird Relay
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

## Environment

```bash
OPENBIRD_COOKIE=your_cookie_string_here
```

The webhook URL is passed to Relay mode on the command line; it is no longer the primary mode switch via `OPENBIRD_WEBHOOK_URL`.

## bot.mjs

```javascript
import express from 'express';
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';

const app = express();
app.use(express.json());

const auth = new FeishuAuth();
auth.prepareAuth(process.env.OPENBIRD_COOKIE);
const api = new FeishuApi();

const { xCsrfToken } = await api.getCsrfToken(auth);
const { userId: myUserId } = await api.getUserInfo(auth, xCsrfToken);
console.log(`Authenticated as ${myUserId}`);

const processed = new Set();

app.post('/webhook', async (req, res) => {
  const event = req.body;
  res.status(200).send('ok');

  if (processed.has(event.event_id)) return;
  processed.add(event.event_id);

  if (event.type !== 'im.message.receive_v1') return;

  const { data } = event;
  if (data.sender.id === myUserId) return;
  if (data.sender.type === 'bot') return;

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

Terminal 2 — Start OpenBird in Relay mode:
```bash
OPENBIRD_COOKIE="..." npx openbird relay http://localhost:3000/webhook
```

## Key Points

1. **Always ACK immediately** — respond 200 before processing, or OpenBird will retry
2. **Deduplicate by event_id** — same event may arrive more than once
3. **Skip own messages** — compare `data.sender.id` against your `myUserId`
4. **Skip bot messages** — check `data.sender.type !== 'bot'` to avoid loops
