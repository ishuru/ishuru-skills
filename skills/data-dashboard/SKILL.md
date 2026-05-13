---
name: Data Dashboard
description: Use when generating an interactive HTML dashboard from live or static data using the Shadow Design System. Renders stat cards with sparklines, canvas charts with hover tooltips, animated counters, and glassmorphism nav. Use when the user says "visualize", "dashboard", "make a dashboard", "render this data", or wants an interactive data surface.
icon: icon.svg
metadata:
  category: mesh/shadow
  family: mesh
  lifecycle: active
  canonical_slug: data-dashboard
  icon_style: craft-category-glyph-v1
---

# Data Dashboard

Generate interactive HTML dashboards from data using the Shadow Design System.

## When to Use

- User says "visualize this data", "make a dashboard", "render dashboard"
- Fetching live data from a source and rendering it interactively
- Any request for an interactive data surface with charts and stats
- Building monitoring, status, or metrics dashboards

## Workflow

1. **Fetch data** — web_fetch, API call, or parse provided data
2. **Read design system** — ALWAYS read `reference/DESIGN-SYSTEM.md` first for tokens
3. **Extract metrics** — identify key stats, trends, categories
4. **Choose sections** — pick from these building blocks:
   - Hero with gradient title + quick stats
   - Stat cards (3-col grid, border-left accent, sparkline, animated counter)
   - Canvas chart (log-scale, bar, line — with hover tooltips)
   - Donut/pie chart (canvas-based)
   - Horizontal race bars
   - Comparison grid
   - Timeline/research cards
   - Footer
5. **Build HTML** — use template at `reference/artifacts/templates/dashboard.html`
6. **Render in browser** — `browser_tool open` → `navigate` → `screenshot` to verify
7. **Release browser** — `browser_tool release` for user exploration

## Design Tokens (Quick Reference)

Source: `reference/DESIGN-SYSTEM.md`

```
Backgrounds:  --bg0:#0a0d12  --bg1:#12161d  --bg2:#1a1e24
Borders:      --bd:#2a2e36   --bdn:#333
Text:         --t0:#e8e6e3   --t1:#9a9a9a   --t2:#5e5e5e   --fg:#f5f5f5
Accent:       --ac:#4a9eff   --ach:#3a8eef  --acd:rgba(74,158,255,.12)
Semantic:     --bl:#00d4ff   --gn:#00ff88   --am:#ffaa00   --rd:#ff4444
Node accents: --au:#c9a227   --ul:#dc143c
Fonts:        system-ui stack + JetBrains Mono (monospace)
Radius:       --r1:4px      --r2:6px
```

## Key Decisions

- **Card accent**: Use `border-left: 3px solid` per DESIGN-SYSTEM.md §6, NOT top bars
- **Section titles**: Monospace, accent color, border-bottom per §6
- **Semantic color mapping**: green=success, red=error, amber=warning, blue=info/link
- **Radius**: Keep tight (4-6px), not rounded (10-16px)
- **Particles**: Use accent color `rgba(74,158,255,...)` not violet

## Gotchas

- **ALWAYS read `reference/DESIGN-SYSTEM.md` before building** — do not use skill-level tokens if the project has its own design system
- Canvas charts need DPR scaling: `cx.setTransform(dpr,0,0,dpr,0,0)` after resize
- Sparkline SVGs: use `stroke-dasharray` + `stroke-dashoffset` animation for draw-in effect
- IntersectionObserver threshold `.08` works well for staggered reveals
- Browser `scroll down` may not work — use `evaluate "window.scrollTo(...)"` instead
- Validate Mermaid diagrams with `mermaid_validate` before embedding in markdown

## Canvas Chart Pattern

```javascript
// DPR-aware canvas setup
const cv = document.getElementById('cc'), cx = cv.getContext('2d'), dpr = devicePixelRatio || 1;
function resize() {
    const rect = cv.parentElement.getBoundingClientRect();
    cv.width = rect.width * dpr;
    cv.height = 300 * dpr;
    cv.style.width = rect.width + 'px';
    cv.style.height = '300px';
    cx.setTransform(dpr, 0, 0, dpr, 0, 0);
    draw();
}
function draw() { /* render logic */ }
resize();
addEventListener('resize', resize);

// Hover tooltip
cv.addEventListener('mousemove', e => {
    const rect = cv.getBoundingClientRect(), mx = e.clientX - rect.left;
    // hit-test data points, show/hide tooltip div
});
```

## Template Location

- Template: `reference/artifacts/templates/dashboard.html`
- Example: `reference/artifacts/examples/epochai-dashboard.html`

## Crystallized From

- Session: 2026-04-26
- Original task: Building an interactive Epoch AI trajectory dashboard from live web data
- Pattern extracted: Fetch → extract → render with design system compliance, canvas charts, animated stats
- Key lesson: Always read the project's DESIGN-SYSTEM.md first — skill tokens are fallbacks only
