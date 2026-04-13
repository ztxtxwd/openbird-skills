---
name: openbird-getting-started
description: |
  Getting started with OpenBird. Load when setting up OpenBird for the first
  time, authenticating, running MCP or Relay mode, or sending a first message.
---

# Getting Started with OpenBird

## Install

```bash
pnpm add openbird
```

Requires Node.js >= 20.

## Authentication

OpenBird uses cookie-based auth from the Feishu web client. You need to extract the cookie string from a logged-in Feishu session.

```javascript
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';

// Step 1: Create auth (NO constructor arguments)
const auth = new FeishuAuth();

// Step 2: Prepare auth with cookie string (MUST call separately)
auth.prepareAuth(process.env.OPENBIRD_COOKIE);

// Step 3: Create API instance (NO constructor arguments)
const api = new FeishuApi();

// Step 4: Bootstrap — get CSRF token and user ID
const { xCsrfToken } = await api.getCsrfToken(auth);
const { userId } = await api.getUserInfo(auth, xCsrfToken);
```

## Send Your First Message

```javascript
// Send a text message (supports Markdown)
const result = await api.sendMessage(auth, 'Hello from OpenBird!', chatId);

// Send with Markdown formatting
await api.sendMessage(auth, '**bold** and *italic*', chatId);
```

## Supported Markdown Syntax

When sending messages, OpenBird auto-converts Markdown:

| Syntax | Result |
|--------|--------|
| `**bold**` | Bold |
| `*italic*` or `_italic_` | Italic |
| `~~strikethrough~~` | Strikethrough |
| `__underline__` | Underline |
| `[text](url)` | Link |
| `> quote` | Block quote |
| `- item` | Unordered list |
| `1. item` | Ordered list |
| `` `code` `` | Inline code |

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENBIRD_COOKIE` | Yes | Cookie string from Feishu web client |
| `OPENBIRD_DEBUG` | No | Set to `true` to enable debug logging |
| `OPENBIRD_ENRICH` | No | In Relay mode, set to `false` to disable event enrichment |

## Running OpenBird

OpenBird has two explicit modes:

```bash
# MCP mode: start only the MCP server over stdio
OPENBIRD_COOKIE="..." npx openbird mcp

# Relay mode: connect WebSocket and forward normalized events to your webhook
OPENBIRD_COOKIE="..." npx openbird relay http://localhost:3000/webhook
```

- `mcp` mode does not start webhook forwarding.
- `relay` mode does not start the MCP server.

## Using as a Library

When you need lower-level programmatic control, you can call the OpenBird library APIs directly:

```javascript
import FeishuAuth from 'openbird/core/auth.js';
import FeishuApi from 'openbird/core/api.js';
import DocxEditor from 'openbird/core/docx-editor.js'; // optional, for document editing

const auth = new FeishuAuth();
auth.prepareAuth(cookieString);

const api = new FeishuApi();
const { xCsrfToken } = await api.getCsrfToken(auth);
const { userId } = await api.getUserInfo(auth, xCsrfToken);

// Now use any api.* method
await api.sendMessage(auth, 'Hello!', chatId);
```
