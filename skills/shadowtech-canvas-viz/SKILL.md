---
name: 'ShadowTech Canvas Viz'
description: Use when generating interactive canvas-based visualizations with ShadowTech design system styling. Fleet topology maps, mesh network graphs, process trees, agent flow diagrams. Deep black backgrounds, violet accent glows, animated particles, glassmorphism tooltips.
icon: icon.svg
user-invocable: false
metadata:
  depends_on: [design-system,shadowtech-design-system]
  category: mesh/shadow
  family: design-system
  lifecycle: active
  canonical_slug: shadowtech-canvas-viz
  icon_style: craft-category-glyph-v1
---

# ShadowTech Canvas Visualization

Create HTML5 Canvas visualizations using ShadowTech design tokens.

## Requirements

1. Load the `shadowtech-design-system` skill first for all tokens
2. Dark background with radial gradient: #0e0e13 center → #07070a edge
3. Grid: rgba(35, 35, 48, 0.2) lines, 40px spacing
4. HiDPI support (devicePixelRatio scaling)
5. requestAnimationFrame render loop
6. Interaction: drag nodes, pan canvas, scroll zoom, click select
7. Touch support for mobile
8. Tooltips: glassmorphism style matching ShadowTech aesthetic
9. Keyboard: Escape=deselect, F=fit view

## Color Palette for Nodes

```javascript
const CAT_COLORS = {
  fleet:    '#f0a050',   // amber — devices, nodes
  ai:      '#b080f0',   // violet — AI, models, inference
  security:'#e86080',   // rose — security, auth
  data:    '#5ca8e8',   // blue — data, storage
  core:    '#8b7cf0',   // accent — services, core infra
  status:  '#4dd4a0',   // green — health, status
};
```

## Canvas Background
```javascript
function drawBackground(ctx, W, H) {
  const bg = ctx.createRadialGradient(W/2, H/2, 0, W/2, H/2, Math.max(W,H)*0.7);
  bg.addColorStop(0, '#0e0e13');
  bg.addColorStop(1, '#07070a');
  ctx.fillStyle = bg;
  ctx.fillRect(0, 0, W, H);
}
```

## Node Rendering
```javascript
function drawNode(ctx, sx, sy, r, color, isSelected, isHovered, isDimmed) {
  // Glow halo
  const glowR = r * (isSelected ? 2.8 : isHovered ? 2.2 : 1.8);
  const glow = ctx.createRadialGradient(sx, sy, r*0.3, sx, sy, glowR);
  const alpha = isDimmed ? 0.02 : isSelected ? 0.3 : isHovered ? 0.18 : 0.08;
  glow.addColorStop(0, color + Math.round(alpha*255).toString(16).padStart(2,'0'));
  glow.addColorStop(1, color + '00');
  ctx.fillStyle = glow;
  ctx.beginPath(); ctx.arc(sx, sy, glowR, 0, Math.PI*2); ctx.fill();

  // Body gradient
  const grad = ctx.createRadialGradient(sx - r*0.25, sy - r*0.25, 0, sx, sy, r);
  grad.addColorStop(0, color);
  grad.addColorStop(1, darken(color, 40));
  ctx.fillStyle = grad;
  ctx.beginPath(); ctx.arc(sx, sy, r, 0, Math.PI*2); ctx.fill();

  // Border
  ctx.strokeStyle = isSelected ? '#fff' : 'rgba(255,255,255,0.12)';
  ctx.lineWidth = isSelected ? 2 : 1;
  ctx.stroke();
}
```

## Edge Rendering (Bezier Curves)
```javascript
// ShadowTech edges: subtle default, violet highlight
ctx.strokeStyle = isHighlighted
  ? 'rgba(139,124,240,0.55)'   // accent
  : 'rgba(100,100,130,0.15)';  // muted
ctx.lineWidth = isHighlighted ? 2 : 1;
ctx.beginPath();
ctx.moveTo(x1, y1);
ctx.quadraticCurveTo(cx, cy, x2, y2);
ctx.stroke();
```

## Particle System
```javascript
// Flow particles along bezier paths
// Color: source node's category color
// Alpha: sin(t * PI) for smooth fade in/out
// Size: highlighted ? 2.5 : 1.5 (scaled by zoom)
```

## Tooltip (Glassmorphism)
```css
#tooltip {
  position: fixed; pointer-events: none;
  background: rgba(14,14,19,0.85);
  backdrop-filter: blur(16px) saturate(1.4);
  border: 1px solid rgba(139,124,240,0.18);
  border-radius: 10px; padding: 12px 16px;
  box-shadow: 0 8px 32px rgba(0,0,0,0.45), 0 0 1px rgba(139,124,240,0.3);
  font-family: 'JetBrains Mono', monospace;
  font-size: 12px; color: #ededf0;
}
.tt-title { font-weight: 700; font-size: 13px; }
.tt-row { display: flex; justify-content: space-between; color: #9e9eb0; }
.tt-val { color: #ededf0; font-variant-numeric: tabular-nums; }
```

## HUD Panel
```css
#hud {
  position: fixed; top: 16px; left: 16px;
  background: rgba(14,14,19,0.72);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255,255,255,0.06);
  border-radius: 10px; padding: 14px 18px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
}
.hud-title { font-size: 14px; font-weight: 700; color: #8b7cf0; letter-spacing: 0.5px; }
.hud-val { color: #ededf0; }
```

## Status Dot
```javascript
// Green: online/healthy/alive  → #4dd4a0
// Amber: idle/pending          → #f0a050
// Red: offline/dead/error      → #e86080
// Pulse animation for healthy nodes
```

## Layout Options
- **Force-directed**: repulsion + spring + gravity + damping
- **Hierarchical/Flowchart**: fixed tier positions with spring-back anchors
- **Radial**: central node with rings of children
- **Grid**: organized rows/columns by category

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

