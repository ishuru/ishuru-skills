---
name: 'ShadowTech HTML Page'
description: Use when generating a standalone HTML page following the ShadowTech design system. Deep black backgrounds, violet accent gradients, glassmorphism panels, fleet cards, terminal blocks. Use for infrastructure dashboards, fleet status pages, agent UIs, and mesh tools.
icon: icon.svg
user-invocable: false
metadata:
  depends_on: [design-system,shadowtech-design-system]
  category: mesh/shadow
  family: design-system
  lifecycle: active
  canonical_slug: shadowtech-html-page
  icon_style: craft-category-glyph-v1
---

# ShadowTech HTML Page Generator

Create standalone HTML pages using the ShadowTech design system.

## Requirements

1. Load the `shadowtech-design-system` skill first to get all tokens
2. Use Inter + JetBrains Mono from Google Fonts
3. Single self-contained HTML file, no external deps beyond fonts
4. Fully responsive with mobile breakpoints at 900px and 600px
5. Support dark mode only (ShadowTech is dark-first)

## HTML Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{TITLE}} — ShadowTech</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=JetBrains+Mono:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #07070a; --surface: #0e0e13; --surface-2: #141418; --surface-3: #1a1a20;
    --border: #232330; --border-subtle: #18181f;
    --text: #ededf0; --text-2: #9e9eb0; --text-3: #606070;
    --accent: #8b7cf0; --accent-bright: #a898ff; --accent-dim: #6050c4;
    --green: #4dd4a0; --amber: #f0a050; --blue: #5ca8e8; --rose: #e86080; --violet: #b080f0;
    --green-dim: rgba(77,212,160,0.10); --amber-dim: rgba(240,160,80,0.10);
    --blue-dim: rgba(92,168,232,0.10); --rose-dim: rgba(232,96,128,0.10);
    --shadow-core: rgba(139,124,240,0.15); --penumbra: rgba(139,124,240,0.06);
    --shadow-grad: linear-gradient(135deg, #8b7cf0, #5ca8e8);
    --r: 10px; --r-lg: 16px; --r-xl: 20px;
    --font: 'Inter', -apple-system, system-ui, sans-serif;
    --mono: 'JetBrains Mono', 'SF Mono', monospace;
    --max-w: 1140px;
  }
  *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    font-family: var(--font); background: var(--bg); color: var(--text);
    line-height: 1.7; -webkit-font-smoothing: antialiased;
  }
  .container { max-width: var(--max-w); margin: 0 auto; padding: 0 32px; }
  /* ... page-specific styles ... */
</style>
</head>
<body>
  <!-- Content here -->
</body>
</html>
```

## Key Patterns

### Page Background with Glow
```css
body::before {
  content: ''; position: fixed; top: -200px; left: 50%;
  transform: translateX(-50%); width: 900px; height: 900px;
  background: radial-gradient(ellipse at 50% 30%, rgba(139,124,240,0.08) 0%, rgba(92,168,232,0.03) 30%, transparent 65%);
  pointer-events: none; z-index: 0;
}
```

### Glassmorphism Panel
```css
.panel {
  background: rgba(14,14,19,0.72);
  backdrop-filter: blur(16px) saturate(1.4);
  -webkit-backdrop-filter: blur(16px) saturate(1.4);
  border: 1px solid var(--border); border-radius: var(--r-lg);
}
```

### Grid Layout
```css
.grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
.grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
.grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 14px; }
@media (max-width: 900px) { .grid-2, .grid-3, .grid-4 { grid-template-columns: 1fr; } }
```

### Scroll Reveal Animation
```js
const observer = new IntersectionObserver(entries => {
  entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); });
}, { threshold: 0.1 });
document.querySelectorAll('.fade-up').forEach(el => observer.observe(el));
```

## Component Catalog

Use these building blocks from the design system:
- **Card** — `.card` with hover lift
- **Card with accent bar** — `::before` top gradient
- **Fleet card** — Device info with status dot
- **Terminal block** — Code display with gradient top bar
- **Stat number** — Large gradient text number
- **Badge/Tag** — Uppercase label
- **Button primary** — Violet with glow
- **Button ghost** — Transparent with border
- **Section divider** — Gradient line
- **Nav** — Glassmorphism fixed header

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

