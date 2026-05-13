---
name: GPT Codex Animation
description: Use when adding gPT Codex style animations. Shimmer sweep, spinner, status elapsed timer, cosine falloff transitions.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: gc-animation
  icon_style: craft-category-glyph-v1
---

Implement animations following gc-design-system patterns.

Available animation patterns:

- **Shimmer sweep**: Cosine-based highlight sweep across text or surfaces
  - Period: 2.0 seconds
  - Falloff: cosine-based (cos(pi * clamped_distance / band_half_width))
  - Band half-width: 5.0 units
  - Blend intensity: 0.9
  - Base color: terminal default fg (web fallback #888888)
  - Highlight color: terminal default bg (web fallback #f5f5f5)
  - CSS implementation:
    ```css
    @keyframes gc-shimmer {
      0% { background-position: -200% 0; }
      100% { background-position: 200% 0; }
    }
    .shimmer {
      background: linear-gradient(90deg, #888 25%, #f5f5f5 50%, #888 75%);
      background-size: 200% 100%;
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      animation: gc-shimmer 2s ease-in-out infinite;
    }
    ```

- **Spinner**: Frame-based status spinner
  - Frame rate: 32ms per frame (~31 fps)
  - Spinner characters cycle through animation frames
  - Renders alongside header text and elapsed timer
  - Detail lines (max 3) prefixed with "  └ "

- **Elapsed timer**: Running duration display
  - Updates every second
  - Format under 60s: "0s", "1s", ... "59s"
  - Format under 1h: "1m 00s", "1m 01s", ...
  - Format 1h+: "1h 02m 03s"
  - Rendered in dim style

- **Dim/bold transitions**: Text emphasis changes
  - Bold for headers and active elements
  - Dim for secondary info, hints, elapsed timers
  - Transition: 150ms ease for web context

- **Fade in**: Element entrance
  - Opacity 0 to 1, 300ms ease
  - Optional translateY(8px) to translateY(0)

- **Stagger entrance**: Sequential child reveals
  - 60ms delay per child element
  - Opacity + translateY animation
  - 300ms duration each

- **Glow pulse**: Accent element emphasis
  - box-shadow oscillation using sin()
  - Color: rgba(16, 163, 127, 0.3) to rgba(16, 163, 127, 0.6)
  - Period: 3s

Timing constants:
- Fast: 150ms (micro-interactions, tooltips)
- Normal: 300ms (hover, focus, state changes)
- Slow: 500ms (layout shifts, emphasis)
- Shimmer: 2000ms (loading sweep cycle)
- Spinner: 32ms (status frame rate)

Always include @media (prefers-reduced-motion: reduce) overrides that:
- Set animation-duration to 0.01ms
- Set transition-duration to 0.01ms
- Remove shimmer and replace with static dim text
- Keep spinner functional but slower (200ms frame rate)

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

