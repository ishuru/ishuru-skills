---
name: GPT Codex Dashboard
description: Use when generating dashboard layout with GPT Codex design. Stats, diffs, terminal output panels, status indicators.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: gc-dashboard
  icon_style: craft-category-glyph-v1
---

Create a dashboard HTML page following gc-design-system.

Layout:
1. Fixed header with title and status indicators (spinner + elapsed timer)
2. Sidebar with navigation/legend (280px, bg #1a1a1a, border-right #333333)
3. Main content area with CSS Grid
4. Stats cards: bg #1a1a1a, border #333333, accent glow #10a37f
5. Use CSS Grid for responsive layout
6. Monospace font (Sohne Mono / Consolas) for data values and code
7. Status dots with green glow animation (#10a37f)
8. Responsive: stack sidebar below on mobile

Components available:
- **Stat card**: label + value + trend indicator, accent border-left #10a37f
- **Status badge**: dot + text, colored by state (green=#10a37f, red=#e74c3c, amber=#f39c12, muted=#888888)
- **Diff view**: split or unified, addition bg #213A2B, deletion bg #4A221D, line numbers in 2-col gutter
- **Terminal output panel**: bg #0a0a0a, monospace, scrollable, green (#10a37f) prompt prefix
- **Status indicator**: animated spinner (32ms frame), header bold, elapsed dim, detail lines with "  └ " prefix (max 3)
- **Progress bar**: gradient fill #10a37f to #0d8c6d with glow
- **Alert/callout**: left border accent color, bg #1a1a1a
- **Data table**: striped rows (#111111 / #0a0a0a), hover highlight rgba(16, 163, 127, 0.1)

$ARGUMENTS

## Pipeline

```
Intent → Select brand tokens → Generate output → Apply theme → Validate render
```

1. Resolve output type (page, dashboard, viz, component, animation)
2. Load brand-specific design tokens from design-system router
3. Generate with correct colors, fonts, spacing
4. Validate output renders correctly

## Modes

| Mode | Output | When |
|------|--------|------|
| `generate` (default) | Full branded output | Normal use |
| `tokens` | Token reference only | "what are the colors" |
| `validate` | Check existing output against tokens | "does this look right" |

## Design Tokens

See parent `design-system` skill for full token table.

## Contract

- Use exact hex colors from design tokens — never approximate
- Use correct font stack for the brand
- All outputs must be standalone HTML (no external dependencies)
- Respect dark/light mode where specified

