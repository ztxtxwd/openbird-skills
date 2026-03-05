# openbird-skills

Agent Skills for [OpenBird](https://github.com/ztxtxwd/openbird) — a Node.js SDK for the Feishu (Lark) IM platform.

These skills teach AI coding agents how to write correct OpenBird code. They follow the [Agent Skills](https://agentskills.io/specification) open standard.

## Install

```bash
npx skills add https://github.com/ztxtxwd/openbird-skills
```

## Skills

| Skill | Description |
|-------|-------------|
| `getting-started` | Install, authenticate, send your first message |
| `api` | All 55+ HTTP API methods (messaging, chats, users, bots, media, calendar) |
| `events` | WebSocket push event types and webhook receiver setup |
| `recipes` | Complete runnable examples (auto-reply bot, scheduled messages, webhook bot) |
| `gotchas` | Known pitfalls and edge cases |

## Structure

```
SKILL.md                              <- Router (loads first)
getting-started/SKILL.md              <- Quick start
api/
  SKILL.md                            <- API method index
  references/
    messaging.md                      <- Send text/file/image, @mention, reply, schedule
    chat-management.md                <- Create group, pin, mark read
    history-search.md                 <- Chat history, search
    users.md                          <- User info, presence, contacts
    reactions-calendar-threads.md     <- Reactions, calendar, threads, urgent
    media.md                          <- Upload/download images and files
    webhook-bots.md                   <- Webhook bot CRUD
events/
  SKILL.md                            <- Event receiving overview
  references/
    event-types.md                    <- All 15+ event payload structures
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
