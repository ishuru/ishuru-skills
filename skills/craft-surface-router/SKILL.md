---
name: Craft Surface Router
description: 'Use when the user needs: Selects the best Craft Agents render surface for agent output: Markdown cards, datatable, spreadsheet, Mermaid, image-preview, html-preview, pdf-preview, or SubmitPlan. Use when deciding how to display results, designing slash-command output, rendering structured data, previews, dashboards, diagrams, or approval handoffs in Craft.'
icon: icon.svg
metadata:
  category: docs/office
  family: craft
  lifecycle: active
  canonical_slug: craft-surface-router
  icon_style: craft-category-glyph-v1
---

# Craft Surface Router

Choose the smallest Craft-native surface that preserves the user's intent and makes the next action obvious.

## Routing table

| Situation | Surface | Why |
|---|---|---|
| One object / latest item | Markdown card | Fast compact state + action rail |
| 2–10 simple objects | Markdown table/cards | Low overhead, readable |
| 10+ rows or sortable data | `datatable` | Native sort/filter |
| Spreadsheet/tracker/finance | `spreadsheet` | Grid semantics, row/col affordance |
| Flow/architecture/state | Mermaid | Visual structure beats prose |
| Screenshot/image/gallery | `image-preview` | Inline + fullscreen visual review |
| PDF artifact | `pdf-preview` | First page + expanded navigation |
| Styled report/email/dashboard | `html-preview` | Rich layout in sandboxed iframe |
| Multi-step work needing approval | `SubmitPlan` | Native review checkpoint |
| Code change explanation | unified diff / code fence | Craft renders diffs/code well |

## Decision algorithm

```text
if user needs approval before execution -> SubmitPlan
else if output is one actionable object -> Markdown card
else if rows > 10 or user may sort/filter -> datatable
else if data is grid-like or workbook-like -> spreadsheet
else if relationship/process is central -> Mermaid
else if visual artifact exists -> image-preview/pdf-preview/html-preview
else -> concise Markdown
```

## Rules

- Prefer rich blocks over prose when the output is structured.
- Prefer Markdown card over `html-preview` for tiny widgets.
- Use `transform_data` for 20+ rows instead of inlining JSON.
- Use `html-preview` only when layout/CSS matters.
- For file-backed rich blocks (`html-preview`, `image-preview`, `pdf-preview`, `datatable`, `spreadsheet`), always emit an absolute `src` path in this Craft Agents harness; relative paths fail with “Only absolute file paths are allowed.”
- Validate complex Mermaid before emitting.
- Always include an action rail or next step.

## Pipeline

```
Input (agent output to render: data, preview, diagram, plan, code)
  ↓
Output Classification (single object / rows / grid / flow / visual / approval)
  ↓
Surface Selection (routing table + decision algorithm)
  ↓
Render + Emit
  ├── Markdown card (default, <8 lines)
  ├── datatable (10+ rows, sortable)
  ├── spreadsheet (grid/tracker)
  ├── Mermaid (flow/architecture)
  ├── image-preview / pdf-preview (visual artifacts)
  ├── html-preview (styled dashboard/report)
  └── SubmitPlan (approval gate)
```

## Modes

### Default: Auto-route
- Apply decision algorithm. Choose smallest surface that fits.
- Emit with action rail or next step.

### compact
- Force Markdown card output regardless of data size. For when the operator wants a quick glance.

### rich
- Prefer rich blocks (datatable, html-preview, Mermaid) over Markdown even for small datasets.

### plan
- Force `SubmitPlan` surface. For multi-step work requiring operator approval before execution.

### dry-run
- Show which surface would be selected and why, without rendering. For designing slash-command output.

## Artifact Routing

| Surface | File-backed? | Path convention |
|---------|-------------|----------------|
| Markdown card | No | Inline in chat |
| `datatable` | Yes (optional) | `data/<slug>.json` via `transform_data` |
| `spreadsheet` | Yes (optional) | `data/<slug>.json` via `transform_data` |
| `html-preview` | Yes | `data/<slug>.html` (absolute path in `src`) |
| `image-preview` | Yes | `data/<slug>.png` (absolute path in `src`) |
| `pdf-preview` | Yes | `data/<slug>.pdf` (absolute path in `src`) |
| Mermaid | No | Inline in chat |
| `SubmitPlan` | Yes | Plan markdown file path |

**Critical:** For file-backed rich blocks, always emit an **absolute** `src` path. Relative paths fail with “Only absolute file paths are allowed.”

## Fallback Chain

When the primary surface fails or is unavailable:

1. `html-preview` → Markdown card
2. `image-preview` → Markdown image link
3. `datatable` → Markdown table
4. `spreadsheet` → Markdown table
5. Mermaid → bullet flow
6. `SubmitPlan` → Markdown plan summary

Always degrade gracefully. Never fail to render output because the preferred surface is unavailable.

## Prerequisites

- Craft Agents harness (for rich block rendering)
- `transform_data` tool (for datatable/spreadsheet with 20+ rows)
- `mcp__session__mermaid_validate` (for complex Mermaid diagrams)
- Filesystem write access for file-backed surfaces

## Error Handling

| Failure | Recovery |
|---------|----------|
| Rich block fails to render | Fall back to Markdown equivalent per fallback chain |
| `transform_data` unavailable | Inline small datasets as Markdown table; note tool limitation |
| Mermaid syntax invalid | Validate with `mermaid_validate`; fix or fall back to bullet flow |
| File write fails for html-preview | Render content inline as Markdown code block; note file write failure |
| Absolute path requirement violated | Fix to absolute path; re-emit |
| Data too large for inline Markdown | Use `transform_data` to write JSON file; emit datatable with `src` |

## Contract

- **Smallest sufficient surface:** Always choose the minimum surface that preserves intent. Do not use `html-preview` when a Markdown card works.
- **Action rails required:** Every rendered output must include an action rail or next step. Never render a dead-end widget.
- **Absolute paths for file-backed blocks:** `html-preview`, `image-preview`, `pdf-preview`, `datatable`, `spreadsheet` must use absolute `src` paths.
- **Mermaid validation:** Validate complex Mermaid diagrams before emitting. Do not emit broken diagrams.
- **`transform_data` for 20+ rows:** Do not inline large JSON. Write to file and reference via `src`.
- **No prose when structure works:** Prefer rich blocks over prose paragraphs for structured data.
- **Widget density:** Keep default cards under ~8 visible lines. Compact surfaces reward the operator more than long prose.

## Fallback order

```text
html-preview -> Markdown card
image-preview -> Markdown image link
datatable -> Markdown table
spreadsheet -> Markdown table
Mermaid -> bullet flow
SubmitPlan -> Markdown plan summary
```
