---
name: 'WhatsApp CLI'
description: Use when accessing whatsApp chats via wacli CLI — search messages, list chats/contacts/groups, send texts and files, sync history, download media. Trigger on WhatsApp, wacli, message search, send message, chat history, WhatsApp contacts, WhatsApp groups.
icon: icon.svg
metadata:
  category: workflow/process
  family: workflow
  lifecycle: active
  canonical_slug: wacli
  icon_style: craft-category-glyph-v1
---

# wacli — WhatsApp CLI

By Peter Steinberger. Go CLI over whatsmeow (WhatsApp Web protocol).
Repo: https://github.com/steipete/wacli

## Quick Reference

```bash
# Auth (interactive — user must scan QR)
wacli auth

# Check status
wacli --json doctor

# Sync continuously (background)
wacli sync --follow

# Search messages
wacli --json messages search "keyword"
wacli --json messages search "keyword" --chat JID --limit 20

# List messages in a chat
wacli --json messages list --chat JID --limit 50

# Show message context
wacli --json messages context --id MSG_ID --chat JID

# List chats
wacli --json chats list --limit 50

# Show specific chat
wacli --json chats show --jid JID

# Search contacts
wacli --json contacts search "name"

# List groups
wacli --json groups list

# Send text
wacli send text --to PHONE_NUMBER --message "text here"

# Send file with caption
wacli send file --to PHONE_NUMBER --file ./path/to/file --caption "description"
wacli send file --to PHONE_NUMBER --file /tmp/file --filename display_name.pdf

# Backfill older history (phone must be online)
wacli history backfill --chat JID --requests 10 --count 50

# Download media from a message
wacli media download --chat JID --id MSG_ID

# Version
wacli version
```

## Important Rules

1. **Always use `--json` for programmatic access** — human-readable output is for display only
2. **Never send messages without explicit user confirmation** — always show recipient + content and ask
3. **JID format:** phone numbers use `COUNTRYCODE+NUMBER@s.whatsapp.net` (e.g., `11234567890@s.whatsapp.net`), groups use `ID@g.us`
4. **Auth is interactive** — if `doctor` shows `authenticated: false`, tell user to run `! wacli auth`
5. **Sync must run separately** — `sync --follow` holds a file lock, can't run concurrent commands
6. **Backfill is best-effort** — WhatsApp may not return full history, phone must be online

## Storage

- Default: `~/.wacli/`
- Contains SQLite DB with FTS5 full-text search
- **Treat as sensitive** — contains full message history

## Common Patterns

### Find recent messages from someone
```bash
wacli --json messages search "keyword" --limit 20 | python3 -m json.tool
```

### List all chats sorted by recent activity
```bash
wacli --json chats list --limit 100
```

### Send and confirm pattern
```
1. User says "send X to Y"
2. Show: "I'll send to [name/number]: [message]. Confirm?"
3. Only after confirmation: wacli send text --to NUMBER --message "text"
```

## Env Vars

- `WACLI_DEVICE_LABEL` — linked device label shown in WhatsApp
- `WACLI_DEVICE_PLATFORM` — platform override (default: CHROME)


## Pipeline

```
Intent → Parse input → Execute API/tool → Structure output → Return
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Formatted result | Normal use |
| `json` | Raw JSON | Programmatic use |

## Artifact Routing

- Output in chat — no persistent artifacts unless operator requests

## Contract

- Never expose API tokens in output
- Respect rate limits

