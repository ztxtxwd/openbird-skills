# Recipe: Scheduled Announcements

Send messages at a scheduled time using OpenBird's schedule message API.

This recipe uses the low-level library API directly. It does not require Relay mode unless your application also needs incoming events.

## Schedule a Single Message

```javascript
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';

const auth = new FeishuAuth();
auth.prepareAuth(process.env.OPENBIRD_COOKIE);
const api = new FeishuApi();

const { xCsrfToken } = await api.getCsrfToken(auth);
await api.getUserInfo(auth, xCsrfToken);

const chatId = '7599271773103737795';

const tomorrow9am = new Date();
tomorrow9am.setDate(tomorrow9am.getDate() + 1);
tomorrow9am.setHours(9, 0, 0, 0);

const result = await api.putScheduleMessage(
  auth,
  '**Good morning!** Daily standup reminder.',
  chatId,
  tomorrow9am.getTime()
);

console.log('Scheduled:', result.scheduleMessage);
```

## Manage Scheduled Messages

```javascript
const pending = await api.pullChatScheduleMessages(auth, chatId);
console.log(pending.scheduleMessages);

await api.patchScheduleMessage(auth, chatId, msgId, 1, {
  scheduleTime: Date.now() + 7200000
});

await api.patchScheduleMessage(auth, chatId, msgId, 1, {
  sendImmediately: true
});

await api.patchScheduleMessage(auth, chatId, msgId, 3);
```

## Recurring Schedule Pattern

OpenBird does not provide built-in recurring schedules. Use your own cron/timer layer:

```javascript
import cron from 'node-cron';

cron.schedule('0 9 * * 1-5', async () => {
  await api.sendMessage(auth, 'Daily standup time!', chatId);
});
```
