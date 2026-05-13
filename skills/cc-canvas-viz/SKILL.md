---
name: Codex Canvas Viz
description: Use when generating an interactive canvas-based visualization with Codex design system styling. Force graphs, particle systems, network maps.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: cc-canvas-viz
  icon_style: craft-category-glyph-v1
---

Create an HTML5 Canvas visualization following cc-design-system.

Requirements:
1. Dark background with subtle radial gradient (#0f1525 center, #080b14 edge)
2. Background grid: rgba(30, 42, 65, 0.3) lines, 40px spacing
3. Node colors by category: cyan #00e5ff, purple #bb86fc, green #69f0ae, orange #ff9100
4. Glow effects: radial gradient halos around nodes
5. Edge rendering: animated flow particles, arrowheads for directed
6. Force simulation: repulsion, spring attraction, center gravity, damping
7. Interaction: drag nodes, pan canvas, scroll zoom, click select
8. Touch support for mobile
9. Tooltips: glassmorphism style (blur bg, rounded, shadow-lg)
10. HiDPI support (devicePixelRatio scaling)
11. requestAnimationFrame loop
12. Keyboard: Escape=deselect, F=fit view

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

