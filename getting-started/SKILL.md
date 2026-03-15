---
name: openbird-getting-started
description: |
  Getting started with OpenBird SDK. Load when setting up OpenBird for the
  first time, authenticating, or sending a first message.
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
| `OPENBIRD_WEBHOOK_URL` | No | URL to receive push events via webhook |

## Running as a Service

OpenBird can run as an MCP server (for AI agents) and optionally forward WebSocket events to a webhook:

```bash
# MCP-only mode
OPENBIRD_COOKIE="..." npx openbird

# MCP + webhook forwarding
OPENBIRD_COOKIE="..." OPENBIRD_WEBHOOK_URL="http://localhost:3000/webhook" npx openbird
```

## Using as a Library

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
