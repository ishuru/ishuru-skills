---
name: Craft Command Surfaces
description: 'Use when the user needs: Designs slash commands as Craft-native command surfaces instead of prose outputs. Use when creating or improving commands like /yt-recent, /meeting-next, /repo-status, /research-queue, or any recurring agent workflow that should render a compact widget with actions.'
icon: icon.svg
metadata:
  depends_on: [craft-surface-router]
  category: craft/commands
  family: craft
  lifecycle: active
  canonical_slug: craft-command-surfaces
  icon_style: craft-category-glyph-v1
---

# Craft Command Surfaces

Every recurring command should return a small operating surface, not a paragraph.

## Contract

```text
Command Surface = Data Extractor + View Renderer + Action Rail + Follow-up Handlers
```

## Required fields

- **Command trigger** — slash command or natural language trigger
- **Data source** — browser/source/files/API/tool
- **Primary object** — what the card represents
- **Default view** — compact render surface
- **Actions** — chips and handler meanings
- **Fallback** — what to render if rich view fails
- **Persistence** — whether to write files, queue research, update status, etc.

## Template

```markdown
## <icon> <Command Name>

|  |  |
|---|---|
| <preview> | **<primary object>**<br><secondary metadata><br>`state` · `state` |

`action` label · `action` label · [open ↗](<target>)
```

## Canonical surfaces

| Command | Default view | Actions |
|---|---|---|
| `/yt-recent` | MediaPreviewCard | `brief` `clip` `claims` `signals` `queue` |
| `/meeting-next` | CalendarCard | `prep` `agenda` `attendees` `risks` `notes` |
| `/repo-status` | RepoStateCard | `diff` `test` `review` `fix` `pr` |
| `/research-queue` | datatable | `rank` `synthesize` `archive` `promote` |
| `/agent-grid` | datatable | `poll` `merge` `message` `stop` |
| `/artifact-recent` | ArtifactCard/gallery | `open` `preview` `promote` `share` |

## Design rules

- One command = one default object unless user asks for a list.
- Lists default to max 10 unless a rich table is requested.
- Preserve a `LastWidgetContext` so short follow-ups work.
- Use `craft-surface-router` before choosing a render form.
- Keep output dense; no post-card explanation unless needed.
