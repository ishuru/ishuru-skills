---
name: GPT Codex Canvas Viz
description: Use when generating canvas-based visualization with GPT Codex design styling. Network graphs, code flow diagrams, dependency maps.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: gc-canvas-viz
  icon_style: craft-category-glyph-v1
---

Create an HTML5 Canvas visualization following gc-design-system.

Requirements:
1. Dark background #0a0a0a with subtle radial gradient (#111111 center, #0a0a0a edge)
2. Background grid: rgba(51, 51, 51, 0.3) lines, 40px spacing
3. Primary node/accent color: #10a37f (OpenAI green)
4. Secondary node colors by category: cyan #00bcd4, amber #f39c12, red #e74c3c, muted #888888
5. Glow effects: radial gradient halos in rgba(16, 163, 127, 0.3) around active nodes
6. Edge rendering: thin lines (#333333), animated flow particles in #10a37f
7. Arrowheads for directed graphs
8. Force simulation: repulsion, spring attraction, center gravity, velocity damping
9. Interaction: drag nodes, pan canvas, scroll zoom, click select
10. Touch support for mobile devices
11. Tooltips: dark bg (#1a1a1a), border #333333, rounded 8px, shadow-lg
12. HiDPI support (devicePixelRatio scaling)
13. requestAnimationFrame render loop
14. Keyboard: Escape=deselect, F=fit view
15. Shimmer effect on loading state before data renders
16. Terminal-adaptive philosophy: minimal chrome, content-forward

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

