# Recipe: Webhook Bot Setup

Create a webhook bot in a Feishu chat and get its webhook URL. This allows external services to push messages into Feishu via HTTP.

## Full Flow

```javascript
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';

const auth = new FeishuAuth();
auth.prepareAuth(process.env.OPENBIRD_COOKIE);
const api = new FeishuApi();

// Bootstrap
const { xCsrfToken } = await api.getCsrfToken(auth);
await api.getUserInfo(auth, xCsrfToken);

const chatId = '7599271773103737795';

// 1. Create the webhook bot
const createResult = await api.createWebhookBot(
  auth,
  chatId,
  'CI/CD Notifier',
  'Posts build results from CI pipeline'
);

if (!createResult.success) {
  console.error('Failed to create bot:', createResult.error);
  process.exit(1);
}

const botId = createResult.data.bot_id;
console.log('Bot created:', botId);

// 2. Get the webhook URL
const infoResult = await api.getWebhookBotInfo(auth, botId);
const webhookUrl = infoResult.data.webhook;
console.log('Webhook URL:', webhookUrl);

// 3. Get signature key (for webhook verification)
const sigResult = await api.getSignature(auth, botId);
console.log('Signature:', sigResult.data);
```

## Sending Messages via Webhook Bot

Once you have the webhook URL, use the built-in send methods (no `auth` needed):

```javascript
// Send a text message
await api.sendWebhookBotText(webhookUrl, 'Build #42 passed!');

// Send an interactive card
await api.sendWebhookBotCard(webhookUrl, {
  header: { title: { tag: 'plain_text', content: 'Build Result' } },
  elements: [
    { tag: 'markdown', content: '**Status**: Passed\n**Branch**: main' }
  ]
});

// Send with webhook signature verification
const sigResult = await api.getSignature(auth, botId);
const secret = sigResult.data.signature;
await api.sendWebhookBotText(webhookUrl, 'Signed message', { secret });
```

## Managing the Bot

```javascript
// Update bot name/description
await api.updateWebhookBot(auth, botId, {
  name: 'Deploy Notifier',
  description: 'Updated description'
});

// Delete bot from chat
await api.deleteBot(auth, chatId, botId);

// Add existing bot to another chat
await api.addBotToChat(auth, anotherChatId, botId);
```
