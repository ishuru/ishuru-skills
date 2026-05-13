---
name: Last Widget Context
description: 'Use when the user needs: Maintains conversational continuity for Craft widget action chips. Use when a previous response rendered a widget/card/table with actions and the user replies with a short command like brief, claims, queue, open, compare, expand, plan, or promote.'
icon: icon.svg
metadata:
  category: craft/runtime
  family: design
  lifecycle: active
  canonical_slug: last-widget-context
  icon_style: craft-category-glyph-v1
---

# Last Widget Context

Treat compact action chips as conversational buttons. If the user responds with a chip name, apply it to the last rendered widget unless ambiguous.

## Mental model

```yaml
last_widget:
  type: <media-card|decision-card|datatable|artifact-gallery|plan-card>
  id: <stable object id>
  source: <where data came from>
  state: <small object state>
  actions:
    <chip>: <handler intent>
```

The state can live in conversation context; do not expose YAML unless useful.

## Default action vocabulary

| Chip | Meaning |
|---|---|
| `brief` | Summarize current object |
| `clip` | Summarize local segment / timestamp |
| `claims` | Extract factual assertions, predictions, benchmarks |
| `signals` | Extract entities, tools, models, workflows, patterns |
| `queue` | Persist/enqueue for later research or work |
| `open` | Provide/deep-link/open target |
| `compare` | Compare to nearest relevant sibling |
| `expand` | Add detail to current widget |
| `plan` | Produce implementation/review plan |
| `promote` | Move artifact to durable/canonical location |
| `watch` | Create a standing monitor/watchlist |

## Resolution rules

1. If user message exactly matches a visible chip, execute that action on the last widget.
2. If multiple recent widgets expose the same chip, ask which one.
3. If the action is destructive or publishes externally, ask for confirmation.
4. If the chip requires unavailable data, fetch it or state the missing prerequisite.
5. After execution, render a new widget or concise result with next chips.

## Example

Previous widget: `/yt-recent` media card for video `RCbJKkG_mZs`.

User: `claims`

Do: fetch transcript, extract claims for that video, render `ClaimsTable` or concise Markdown if few claims.
