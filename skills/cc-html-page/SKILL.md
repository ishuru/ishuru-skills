---
name: 'Codex HTML Page'
description: Use when generating a standalone HTML page following Codex design system. Use when creating visualizations, dashboards, tools, or any browser-based output.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: cc-html-page
  icon_style: craft-category-glyph-v1
---

Generate a self-contained HTML file using the cc-design-system tokens.

Requirements:
1. Load the design system from ~/.Codex/skills/cc-design-system/SKILL.md for reference
2. Use dark theme (#0a0a0a background, #f5f5f5 text)
3. System font stack for body, monospace for code
4. Apply proper spacing scale (4/8/12/16/20/24/32px)
5. Border-radius: 10px for cards, 8px for smaller elements
6. Glassmorphism panels: rgba bg + backdrop-filter blur(12px)
7. Glow effects for accent elements
8. Responsive: works on xl/lg/md/sm breakpoints
9. Single file, no external dependencies
10. Include prefers-reduced-motion support

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

