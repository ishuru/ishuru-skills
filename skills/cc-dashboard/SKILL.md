---
name: Codex Dashboard
description: Use when generating a dashboard layout with Codex design system. Stats panels, charts, status indicators.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: cc-dashboard
  icon_style: craft-category-glyph-v1
---

Create a dashboard HTML page following cc-design-system.

Layout:
1. Fixed header with title and status indicators
2. Sidebar with navigation/legend (280px, bg #252542)
3. Main content area with CSS Grid
4. Stats cards: glassmorphism, glow accent borders
5. Use CSS Grid for responsive layout
6. Monospace font for data values
7. Status dots with glow animation
8. Responsive: stack sidebar below on mobile

Components available:
- Stat card: label + value + trend indicator
- Status badge: dot + text, colored by state
- Data table: striped rows, hover highlight
- Progress bar: gradient fill with glow
- Alert/callout: left border accent color

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

