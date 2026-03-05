# Webhook Receiver Setup (openbird-webhook-node)

`openbird-webhook-node` is a companion package that provides a ready-made Express server for receiving OpenBird webhook events.

## Install

```bash
pnpm add openbird-webhook-node
```

## Basic Usage

```javascript
import { createWebhookServer } from 'openbird-webhook-node';

const server = createWebhookServer({
  port: 3000,
  onEvent: async (event) => {
    console.log('Received event:', event.type);

    if (event.type === 'im.message.receive_v1') {
      const { data } = event;
      console.log(`Message from ${data.sender.id}: ${data.content.text}`);
    }
  }
});
```

Then point OpenBird to this server:

```bash
OPENBIRD_WEBHOOK_URL="http://localhost:3000/webhook" npx openbird
```

## Event Handling Pattern

```javascript
const server = createWebhookServer({
  port: 3000,
  onEvent: async (event) => {
    switch (event.type) {
      case 'im.message.receive_v1':
        await handleMessage(event.data);
        break;
      case 'im.message.reaction_v1':
        await handleReaction(event.data);
        break;
      case 'im.message.urgent_v1':
        await handleUrgent(event.data);
        break;
      default:
        console.log('Unhandled event:', event.type);
    }
  }
});
```

## Idempotency

The same event may be delivered more than once (retry on failure). Use `event.event_id` to deduplicate:

```javascript
const processed = new Set();

onEvent: async (event) => {
  if (processed.has(event.event_id)) return;
  processed.add(event.event_id);
  // handle event...
}
```
