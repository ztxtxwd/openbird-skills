# openbird-skills

Agent Skills for [OpenBird](https://github.com/ztxtxwd/openbird) — a local Feishu (Lark) infrastructure service that runs in either MCP mode or Relay mode.

These skills teach AI coding agents how to use current OpenBird commands, MCP tools, and low-level library APIs correctly. They follow the [Agent Skills](https://agentskills.io/specification) open standard.

## Install

```bash
npx skills add https://github.com/ztxtxwd/openbird-skills
```

## Skills

| Skill | Description |
|-------|-------------|
| `getting-started` | Install, authenticate, run OpenBird in MCP or Relay mode, and send your first message |
| `api` | Messaging, chats, users, bots, media, calendar, documents, meeting rooms, and related low-level APIs |
| `events` | Relay-mode event delivery, webhook receiver setup, and event payload reference |
| `recipes` | Runnable examples for common OpenBird workflows |
| `gotchas` | Known pitfalls and edge cases |

## Structure

```
SKILL.md                              <- Router (loads first)
getting-started/SKILL.md              <- Quick start, modes, auth
api/
  SKILL.md                            <- Capability index
  references/
    messaging.md                      <- Send, reply, forward, schedule, message links
    chat-management.md                <- Create group, pin, mark read, session metadata
    history-search.md                 <- Chat history, search
    users.md                          <- User info, profile, relation, contacts
    reactions-threads-urgent.md       <- Reactions, threads, urgent, Baike, config
    calendar.md                       <- Calendar, RSVP, minutes, sharing, meeting rooms
    media.md                          <- Upload/download images and files
    webhook-bots.md                   <- Webhook bot CRUD and send methods
    documents.md                      <- Read/edit Feishu documents (wiki/docx)
events/
  SKILL.md                            <- Relay event receiving overview
  references/
    event-types.md                    <- Event payload structures and special cases
    webhook-node-setup.md             <- openbird-webhook-node setup
recipes/
  SKILL.md                            <- Recipe index
  references/
    auto-reply-bot.md                 <- Receive + reply (full example)
    scheduled-announcements.md        <- Schedule messages
    webhook-bot-setup.md              <- Create webhook bot end-to-end
gotchas/
  SKILL.md                            <- All pitfalls in one place
```

## License

MIT
