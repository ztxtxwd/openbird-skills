---
name: openbird
description: |
  OpenBird SDK for Feishu (Lark) IM platform. Load when writing code that
  uses openbird to send messages, manage chats, handle events, create bots,
  upload files, or interact with Feishu in any way.
---

# OpenBird

OpenBird is a Node.js SDK for the Feishu (Lark) IM platform. It provides two channels:

- **HTTP API (send)**: All outgoing actions — send messages, manage chats, search users, upload files, manage bots, edit documents, manage calendar — go through `FeishuApi` class methods.
- **Document Editor**: Stateful `DocxEditor` class for reading and editing Feishu documents (insert blocks, edit text, delete, move, change type).
- **WebSocket (receive only)**: Server push events (incoming messages, reactions, read state, urgent notifications, calendar sync) are received via WebSocket and forwarded to your webhook URL.

## Decision Tree

```
What do you need?
|
+-- Send messages, manage chats, query users
|   -> Load: getting-started + api
|
+-- Receive incoming messages / events
|   -> Load: getting-started + events
|
+-- Build a complete bot (send + receive)
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
+-- Create / update / delete calendar events, book meeting rooms
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
| `getting-started` | Install, authenticate, send your first message |
| `api` | All 75+ HTTP API methods organized by domain |
| `events` | WebSocket event types and webhook setup |
| `recipes` | Complete runnable examples for common scenarios |
| `gotchas` | Known pitfalls and edge cases |

## Quick Reference

```
Package:  openbird (npm)
Runtime:  Node.js >= 20
Manager:  pnpm
Auth:     Cookie-based (from Feishu web client)
Env vars: OPENBIRD_COOKIE (required), OPENBIRD_WEBHOOK_URL (optional)
```
