# Recipe: Scheduled Announcements

Send messages at a scheduled time using OpenBird's schedule message API.

## Schedule a Single Message

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

// Schedule for tomorrow 9:00 AM
const tomorrow9am = new Date();
tomorrow9am.setDate(tomorrow9am.getDate() + 1);
tomorrow9am.setHours(9, 0, 0, 0);

const result = await api.putScheduleMessage(
  auth,
  '**Good morning!** Daily standup reminder.',
  chatId,
  tomorrow9am.getTime()  // milliseconds
);

console.log('Scheduled:', result.scheduleMessage);
```

## Manage Scheduled Messages

```javascript
// List all pending scheduled messages in a chat
const pending = await api.pullChatScheduleMessages(auth, chatId);
console.log(pending.scheduleMessages);

// Reschedule
await api.patchScheduleMessage(auth, chatId, msgId, 1, {
  scheduleTime: Date.now() + 7200000  // 2 hours from now
});

// Send immediately (don't wait for scheduled time)
await api.patchScheduleMessage(auth, chatId, msgId, 1, {
  sendImmediately: true
});

// Cancel (delete)
await api.patchScheduleMessage(auth, chatId, msgId, 3);
```

## Recurring Schedule Pattern

OpenBird doesn't have built-in recurring schedules. Use `setInterval` or a cron library:

```javascript
import cron from 'node-cron';

// Every weekday at 9:00 AM
cron.schedule('0 9 * * 1-5', async () => {
  await api.sendMessage(auth, 'Daily standup time!', chatId);
});
```
