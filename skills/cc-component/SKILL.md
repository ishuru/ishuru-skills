---
name: Codex Component
description: Use when generating individual UI components following Codex design system. Buttons, cards, modals, tooltips, forms.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: cc-component
  icon_style: craft-category-glyph-v1
---

Generate a UI component following cc-design-system tokens.

Available components:
- **Button**: primary/secondary/tertiary, with icon support
- **Card**: elevated panel with optional header, glassmorphism
- **Modal**: centered overlay, backdrop blur, scale-in animation
- **Tooltip**: positioned popup, 320px max, arrow indicator
- **Form input**: dark bg, border glow on focus, label above
- **Badge**: pill shape, category colored with glow
- **Table**: striped, hover rows, sortable headers
- **Tabs**: underline active indicator, smooth transition
- **Accordion**: chevron rotation, height animation
- **Toast**: slide-in notification, auto-dismiss

All components must:
1. Follow the token system exactly
2. Include hover, focus, active states
3. Support prefers-reduced-motion
4. Use proper semantic HTML

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

