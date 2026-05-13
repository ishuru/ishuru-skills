---
name: Codex Animation
description: Use when adding codex style animations to HTML elements. Typing effects, glow pulses, particle systems, status indicators.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: cc-animation
  icon_style: craft-category-glyph-v1
---

Implement animations following cc-design-system patterns.

Available animation patterns:
- **Status glyph rotation**: cycle through ◈ ◓ ✽ ◐ ◑ ◒ ● ◯ ◇ ◆ ▲ ▼ at 1000ms
- **Typing effect**: 100ms per character, 50ms deletion, 2000ms pause
- **Glow pulse**: subtle radius oscillation using sin(), 3-5s period
- **Particle drift**: slow-moving dots, connect when near (constellation effect)
- **Stagger entrance**: 80ms delay per child, opacity + translateY
- **Scale-in**: 0.95 to 1.0 with opacity, 180ms ease
- **Clip-path reveal**: inset(0 0 100% 0) to inset(0), 600ms expo
- **Gradient shimmer**: moving highlight across surface

Timing:
- Fast: 180ms (tooltips, micro-interactions)
- Normal: 300ms (hover, focus, state changes)
- Slow: 500ms (layout shifts, emphasis)
- Menu: 600ms open, 300ms close (cubic-bezier 0.16,1,0.3,1)

Always include @media (prefers-reduced-motion: reduce) overrides.

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

