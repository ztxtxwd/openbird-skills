# Webhook Receiver Setup (openbird-webhook-node)

`openbird-webhook-node` is a companion package that provides a ready-made Node server for receiving OpenBird Relay events.

## Install

```bash
pnpm add openbird-webhook-node
```

## Basic Usage

```javascript
import { createServer } from 'openbird-webhook-node'

createServer({
  port: 3000,
  onMessage(event) {
    console.log('Received event:', event.type)

    if (event.type === 'im.message.receive_v1') {
      const { data } = event
      console.log(`Message from ${data.sender.id}: ${data.content.text}`)
    }
  },
})
```

Then point OpenBird Relay to this server:

```bash
OPENBIRD_COOKIE="..." npx openbird relay http://localhost:3000/webhook
```

## Event Handling Pattern

```javascript
createServer({
  port: 3000,
  onMessage(event) {
    switch (event.type) {
      case 'im.message.receive_v1':
        handleMessage(event.data)
        break
      case 'im.message.reaction_v1':
        handleReaction(event.data)
        break
      case 'im.message.urgent_v1':
        handleUrgent(event.data)
        break
      default:
        console.log('Unhandled event:', event.type)
    }
  },
})
```

## Idempotency

The same event may be delivered more than once (retry on failure). Use `event.event_id` to deduplicate:

```javascript
const processed = new Set()

function onMessage(event) {
  if (processed.has(event.event_id)) return
  processed.add(event.event_id)
  // handle event...
}
```
