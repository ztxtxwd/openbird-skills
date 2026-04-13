---
name: openbird-api
description: |
  OpenBird capability reference. Load when writing code that uses OpenBird
  through FeishuApi, DocxEditor, or equivalent MCP-exposed capabilities for
  messaging, chats, users, media, bots, calendar, documents, and related
  Feishu operations.
---

# OpenBird API Reference

OpenBird exposes Feishu capabilities in two closely related forms:

1. **Low-level library APIs** via `FeishuApi`
2. **Stateful document editing** via `DocxEditor`
3. **MCP tools** that map to the same capability areas in current OpenBird releases

All low-level outgoing operations use `FeishuApi` class methods. Every method takes `auth` as its first argument.

```javascript
import FeishuApi from 'openbird/core/api.js';
const api = new FeishuApi();
```

For document editing, use the stateful `DocxEditor` wrapper:

```javascript
import DocxEditor from 'openbird/core/docx-editor.js';
```

## Capability Index

### What do you want to do?

```
Send a message (text, file, image, @mention, reply, forward, message link)?
  -> references/messaging.md

Schedule a message for later?
  -> references/messaging.md (Schedule Message section)

Create a group, pin a session, mark read, or inspect session metadata?
  -> references/chat-management.md

Get chat history or search messages?
  -> references/history-search.md

Look up user info, set signature, inspect relation, or manage contacts?
  -> references/users.md

Add emoji reactions, create threads, send urgent, look up Baike terms?
  -> references/reactions-threads-urgent.md

Create / update / delete calendar events, RSVP, create meeting minutes, share events, or find meeting rooms?
  -> references/calendar.md

Upload or download images/files?
  -> references/media.md

Create or manage webhook bots, or send messages via webhook bot URL?
  -> references/webhook-bots.md

Read or edit Feishu documents (wiki / docx)?
  -> references/documents.md
```

## Common Patterns

Most API methods follow this pattern:

```javascript
const result = await api.methodName(auth, ...args);
// result: { success: boolean, data?: any, error?: string }
```

Some older messaging methods still return `{ data, status }` instead:
- `sendMessage`
- `sendFileMessage`
- `sendImageMessage`
- `sendMentionMessage`
- `sendReply`

These return raw protobuf response buffers.
