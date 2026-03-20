---
name: openbird-api
description: |
  OpenBird HTTP API reference. Load when writing code that calls FeishuApi
  methods — sending messages, managing chats, querying users, uploading
  files, managing bots, scheduling messages, editing documents, managing
  calendar events, or any Feishu operation.
---

# OpenBird API Reference

All outgoing operations use `FeishuApi` class methods. Every method takes `auth` as its first argument.

```javascript
import FeishuApi from 'openbird/core/api.js';
const api = new FeishuApi();
```

For document editing, use the stateful `DocxEditor` wrapper:

```javascript
import DocxEditor from 'openbird/core/docx-editor.js';
```

## Method Index

### What do you want to do?

```
Send a message (text, file, image, @mention, reply, forward)?
  -> references/messaging.md

Schedule a message for later?
  -> references/messaging.md (Schedule Message section)

Create a group, pin a session, mark read?
  -> references/chat-management.md

Get chat history or search messages?
  -> references/history-search.md

Look up user info, set signature, manage contacts?
  -> references/users.md

Add emoji reactions, create threads, send urgent, look up Baike terms?
  -> references/reactions-threads-urgent.md

Create / update / delete calendar events, RSVP, meeting rooms?
  -> references/calendar.md

Upload or download images/files?
  -> references/media.md

Create or manage webhook bots, or send messages via webhook bot URL?
  -> references/webhook-bots.md

Read or edit Feishu documents (wiki / docx)?
  -> references/documents.md
```

## Common Patterns

Every API method follows the same pattern:

```javascript
const result = await api.methodName(auth, ...args);
// result: { success: boolean, data?: any, error?: string }
```

Some older methods return `{ data, status }` instead (sendMessage, sendFileMessage, sendImageMessage, sendMentionMessage, sendReply). These return raw protobuf response buffers.
