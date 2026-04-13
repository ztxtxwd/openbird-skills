---
name: openbird
description: |
  OpenBird for Feishu (Lark). Load when writing code that uses OpenBird in
  MCP mode, Relay mode, or through its low-level FeishuApi / DocxEditor
  interfaces to send messages, manage chats, receive events, edit documents,
  manage calendar, or work with webhook bots.
---

# OpenBird

OpenBird is a local Feishu (Lark) infrastructure service with two explicit run modes:

- **MCP mode**: starts a local MCP server over stdio so AI agents can call OpenBird tools.
- **Relay mode**: connects to Feishu WebSocket, normalizes incoming events, and POSTs them to your webhook URL.
- **Low-level library APIs**: `FeishuApi` and `DocxEditor` remain available when you need direct programmatic access.

## Decision Tree

```
What do you need?
|
+-- Let an AI agent call Feishu tools through MCP
|   -> Load: getting-started + api
|
+-- Receive incoming messages / events in your own webhook service
|   -> Load: getting-started + events
|
+-- Build a complete bot (receive in Relay mode, send via API/MCP)
|   -> Load: getting-started + api + events + recipes
|
+-- Create / manage webhook bots, or send messages via webhook bot URL
|   -> Load: getting-started + api (see references/webhook-bots.md)
|
+-- Upload images or files
|   -> Load: getting-started + api (see references/media.md)
|
+-- Schedule messages for future delivery
|   -> Load: getting-started + api (see references/messaging.md)
|
+-- Create / update / delete calendar events, book meeting rooms, or generate meeting minutes
|   -> Load: getting-started + api (see references/calendar.md)
|
+-- Read or edit Feishu documents (wiki / docx)
|   -> Load: getting-started + api (see references/documents.md)
|
+-- Look up Feishu Lingo (飞书词典) terms
|   -> Load: getting-started + api (see references/reactions-threads-urgent.md)
```

## Skills Overview

| Skill | What it covers |
|-------|---------------|
| `getting-started` | Install, authenticate, and run OpenBird in MCP or Relay mode |
| `api` | OpenBird capabilities by domain: messaging, chats, users, bots, media, calendar, documents |
| `events` | Relay-mode event types and webhook setup |
| `recipes` | Complete runnable examples for common scenarios |
| `gotchas` | Known pitfalls and edge cases |

## Quick Reference

```
Package:  openbird (npm)
Runtime:  Node.js >= 20
Manager:  pnpm
Auth:     Cookie-based (from Feishu web client)
Modes:    mcp | relay <webhook-url>
Env vars: OPENBIRD_COOKIE (required), OPENBIRD_DEBUG (optional), OPENBIRD_ENRICH (optional)
```
