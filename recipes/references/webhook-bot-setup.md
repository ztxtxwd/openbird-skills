# Recipe: Webhook Bot Setup

Create a webhook bot in a Feishu chat and get its webhook URL. This allows external services to push messages into Feishu via HTTP.

## Full Flow

```javascript
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';

const auth = new FeishuAuth();
auth.prepareAuth(process.env.OPENBIRD_COOKIE);
const api = new FeishuApi();

const { xCsrfToken } = await api.getCsrfToken(auth);
await api.getUserInfo(auth, xCsrfToken);

const chatId = '7599271773103737795';

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

const infoResult = await api.getWebhookBotInfo(auth, botId);
const webhookUrl = infoResult.data.webhook;
console.log('Webhook URL:', webhookUrl);

const sigResult = await api.getSignature(auth, botId);
console.log('Signature:', sigResult.data);
```

## Sending Messages via Webhook Bot

Once you have the webhook URL, use the built-in send methods (no `auth` needed). These correspond to current MCP tools `send_webhook_bot_text`, `send_webhook_bot_post`, `send_webhook_bot_image`, `send_webhook_bot_share_chat`, `send_webhook_bot_card`, and `send_webhook_bot_message`.

```javascript
await api.sendWebhookBotText(webhookUrl, 'Build #42 passed!');

await api.sendWebhookBotCard(webhookUrl, {
  header: { title: { tag: 'plain_text', content: 'Build Result' } },
  elements: [
    { tag: 'markdown', content: '**Status**: Passed\n**Branch**: main' }
  ]
});

const sigResult = await api.getSignature(auth, botId);
const secret = sigResult.data.signature;
await api.sendWebhookBotText(webhookUrl, 'Signed message', { secret });
```

## Managing the Bot

```javascript
await api.updateWebhookBot(auth, botId, {
  name: 'Deploy Notifier',
  description: 'Updated description'
});

await api.deleteBot(auth, chatId, botId);
await api.addBotToChat(auth, anotherChatId, botId);
```
