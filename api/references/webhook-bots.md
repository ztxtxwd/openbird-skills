# Webhook Bot Management API

These APIs manage webhook bots in Feishu chats. They use HTTP JSON (`open.feishu.cn`), not the protobuf gateway.

## Create Webhook Bot

```javascript
const result = await api.createWebhookBot(auth, chatId, name, description, options?)
// result: { success, data: { bot_id: '7611809280296569817' } }
```

| Param | Type | Description |
|-------|------|-------------|
| `chatId` | string | Chat ID to create the bot in |
| `name` | string | Bot name |
| `description` | string | Bot description |
| `options.avatarKey` | string? | Avatar image key |

## Get Webhook Bot Info

```javascript
const result = await api.getWebhookBotInfo(auth, botId)
// result: { success, data: { bot_id, webhook, signature, name, ... } }
```

Returns the bot's webhook URL in `result.data.webhook`.

## Update Webhook Bot

```javascript
await api.updateWebhookBot(auth, botId, { name?, description?, avatarKey? })
```

## Get / Reset Signature

```javascript
const result = await api.getSignature(auth, botId)
```

## Delete Bot from Chat

```javascript
await api.deleteBot(auth, chatId, botId)
```

## Add Existing Bot to Chat

```javascript
await api.addBotToChat(auth, chatId, botId)
```

## Update App Bot Info

```javascript
await api.updateAppBotInfo(auth, chatId, botId, { checkMender? })
```

Toggle whether admin approval is required.

## Fetch Bot Configuration

```javascript
await api.fetchBotAddConf(auth, botId)
await api.fetchBotConfResource(auth, botId, chatId)
```

## List / Search Bots

```javascript
// List bots available to add to a chat
const result = await api.listCandidateBots(auth, chatId)
// result: { success, data: { bots, sharable_bots } }

// Search for bots by keyword
const result = await api.searchBots(auth, chatId, 'webhook')
// result: { success, data: { bots, recommend_bots, sharable_bots } }
```

## Complete Webhook Bot Setup Flow

```javascript
// 1. Create bot in a chat
const createResult = await api.createWebhookBot(
  auth, chatId, 'My Notification Bot', 'Sends notifications'
);
const botId = createResult.data.bot_id;

// 2. Get the webhook URL
const infoResult = await api.getWebhookBotInfo(auth, botId);
const webhookUrl = infoResult.data.webhook;
console.log('Webhook URL:', webhookUrl);

// 3. (Optional) Get signature for webhook verification
const sigResult = await api.getSignature(auth, botId);

// Now POST JSON to webhookUrl to send messages through the bot
```
