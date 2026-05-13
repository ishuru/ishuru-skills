---
name: 'GPT Codex HTML Page'
description: Use when generating a standalone HTML page following GPT Codex design system. Clean, minimal OpenAI aesthetic with semantic colors.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: gc-html-page
  icon_style: craft-category-glyph-v1
---

Generate a self-contained HTML file using the gc-design-system tokens.

Requirements:
1. Load the design system from ~/.Codex/skills/gc-design-system/SKILL.md for reference
2. Use dark theme (#0a0a0a background, #f5f5f5 text)
3. System font stack for body, Sohne Mono / Consolas for code
4. Apply proper spacing scale (4/8/12/16/20/24/32px)
5. Border-radius: 12px for cards, 8px for smaller elements
6. Accent color: #10a37f (OpenAI green) for links, buttons, focus rings
7. Hover accent: #0d8c6d
8. Selection highlight: rgba(16, 163, 127, 0.3)
9. Muted text: #888888, secondary bg: #1a1a1a, borders: #333333
10. Shimmer loading effect for async content (2s period, cosine sweep)
11. Responsive: works across desktop, tablet, and mobile
12. Single file, no external dependencies
13. Include prefers-reduced-motion support
14. Include prefers-color-scheme support with light theme variant:
    - Light bg: #ffffff, text: #1a1a1a, border: #d9d9d9
    - Accent remains #10a37f

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

