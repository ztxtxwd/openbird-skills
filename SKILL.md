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

## Baseline

Current docs are aligned against OpenBird `v3.2.5` (`f38cf350ee0834a5c65d9d9ad5893139d8892fda`).

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
+-- Manage chats or groups, inspect pins, or sync contact aliases
|   -> Load: getting-started + api (see references/chat-management.md and references/users.md)
|
+-- Read chat or thread history, optionally materialize local media files
|   -> Load: getting-started + api (see references/history-search.md)
|
+-- Create / manage webhook bots, or send messages via webhook bot URL
|   -> Load: getting-started + api (see references/webhook-bots.md)
|
+-- Upload images/files or download message attachments to a local temp path
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
| `api` | OpenBird capabilities by domain: messaging, groups/chats, users/contacts, media, calendar, documents, webhook bots |
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
