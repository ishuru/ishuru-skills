# gh-recent — Guide

## Quick Start

```bash
# Resolve your most recent GitHub activity
@github recent
```

## Modes

| Mode | Description |
|------|-------------|
| Default | One-paragraph summary of most recent repo/event |
| `install` | Resolve → fetch agent files → create skill → validate → use |
| `context` | Fetch SKILL.md/AGENTS.md/CLAUDE.md/README.md to /tmp |
| `skill` | Same as install, explicitly for skill creation |
| `starred` | List recent starred repos with timestamps |
| `full` | Complete activity log with all event types |
| `raw` | Unprocessed gh api JSON output |

## Multi-Account

`gh` resolves to the authenticated account. Specify an alternate user:

```
gh-recent ishuru
```

## Fallback Chain

1. `gh` CLI API (primary)
2. Browser surface bridge (if gh unavailable)
3. Web search (if both fail)
4. Ask operator to specify repo

## Output Contract

Every invocation includes:

```
Resolved via gh: <repo> — <event type/action/time>
Action: <summarized/opened/installed/queued>
Skill: <path when created, else none>
Re-skill delta: <none | trigger/fallback/workflow patch>
Validation: <passed/failed/not run>
Used it: <one concrete command/result>
```
