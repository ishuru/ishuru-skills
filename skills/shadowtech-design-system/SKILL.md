---
name: Shadowtech Design System
description: 'Use when the user needs: ShadowTech design system tokens and patterns. The official dark-theme design language for ShadowTech fleet infrastructure, agent platforms, and mesh dashboards. Use when building any UI, visualization, or HTML output that should match the ShadowTech brand aesthetic — deep blacks, violet accents, gradient glows, glassmorphism cards.'
icon: icon.svg
user-invocable: false
metadata:
  category: mesh/shadow
  family: design-system
  lifecycle: active
  canonical_slug: shadowtech-design-system
  icon_style: craft-category-glyph-v1
---

# ShadowTech Design System

## Color Tokens

### Base (Backgrounds)
```
--bg:           #07070a     /* deepest black - page background */
--surface:      #0e0e13     /* card/panel background */
--surface-2:    #141418     /* elevated surface, code blocks */
--surface-3:    #1a1a20     /* highest elevation, hover states */
```

### Borders
```
--border:        #232330     /* standard border */
--border-subtle: #18181f     /* subtle dividers */
```

### Text
```
--text:   #ededf0     /* primary text */
--text-2: #9e9eb0     /* secondary / descriptions */
--text-3: #606070     /* muted / labels / placeholders */
```

### Accent (Primary — Violet)
```
--accent:        #8b7cf0     /* primary interactive */
--accent-bright: #a898ff     /* hover / highlight */
--accent-dim:    #6050c4     /* active / pressed */
```

### Semantic Colors
```
--green:     #4dd4a0     /* success, online, healthy */
--green-dim: rgba(77,212,160,0.10)
--amber:     #f0a050     /* warning, idle, pending */
--amber-dim: rgba(240,160,80,0.10)
--blue:      #5ca8e8     /* info, links, data */
--blue-dim:  rgba(92,168,232,0.10)
--rose:      #e86080     /* error, offline, danger */
--rose-dim:  rgba(232,96,128,0.10)
--violet:    #b080f0     /* special, premium, AI */
--violet-dim: rgba(176,128,240,0.10)
```

### Shadow & Glow
```
--shadow-core: rgba(139,124,240,0.15)   /* primary glow */
--penumbra:    rgba(139,124,240,0.06)   /* subtle ambient */
--umbra:       rgba(7,7,10,0.95)        /* deep overlay */
--shadow-grad: linear-gradient(135deg, #8b7cf0, #5ca8e8)  /* signature gradient */
```

### ShadowSkills Category Mapping
```
Fleet / Mesh:      --amber (#f0a050)
AI / Compute:      --violet (#b080f0)
Security:          --rose (#e86080)
Data / Storage:    --blue (#5ca8e8)
Core / Services:   --accent (#8b7cf0)
Status / Health:   --green (#4dd4a0)
```

## Typography
```
--font: 'Inter', -apple-system, system-ui, sans-serif
--mono: 'JetBrains Mono', 'SF Mono', monospace
```

### Scale
```
xs:    11px
sm:    13px
base:  14-15px
lg:    16-17px
xl:    22px
2xl:   clamp(26px, 3.5vw, 36px)
3xl:   clamp(38px, 5.5vw, 68px)
```

### Weights
```
Regular: 400   (body)
Medium:  500   (nav links)
Semibold: 600  (buttons, labels)
Bold:    700   (headings)
Heavy:   750-780 (hero titles)
Black:   800   (stat numbers)
```

## Border Radius
```
--r:    10px    /* standard */
--r-lg: 16px    /* cards, panels */
--r-xl: 20px    /* hero CTA, large panels */
pill:   20px    /* pills, tags */
small:  5-7px   /* badges, inner elements */
```

## Layout
```
--max-w: 1140px     /* content max width */
padding: 0 32px     /* container horizontal padding */
gap: 16-24px        /* grid gaps */
section-padding: 100px 0   /* vertical section spacing */
```

## Component Patterns

### Card
```css
.card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  padding: 32px 24px;
  transition: all 0.25s;
}
.card:hover {
  border-color: #333;
  transform: translateY(-2px);
  box-shadow: 0 8px 32px rgba(0,0,0,0.3);
}
```

### Card with Top Accent Bar
```css
.card-accent {
  position: relative;
  overflow: hidden;
}
.card-accent::before {
  content: '';
  position: absolute;
  top: 0; left: 0; right: 0;
  height: 2px;
  background: var(--card-color, var(--accent));
}
```

### Button Primary
```css
.btn-primary {
  background: var(--accent);
  color: #fff;
  padding: 12px 24px;
  border-radius: 8px;
  font-weight: 600;
  box-shadow: 0 0 20px rgba(139,124,240,0.15), 0 2px 8px rgba(0,0,0,0.3);
}
.btn-primary:hover {
  background: var(--accent-dim);
  transform: translateY(-1px);
  box-shadow: 0 0 32px rgba(139,124,240,0.25), 0 4px 12px rgba(0,0,0,0.4);
}
```

### Button Ghost
```css
.btn-ghost {
  background: transparent;
  color: var(--text);
  border: 1px solid var(--border);
  padding: 12px 24px;
  border-radius: 8px;
}
.btn-ghost:hover {
  background: var(--surface-2);
  border-color: #333;
  transform: translateY(-1px);
}
```

### Glassmorphism Nav
```css
nav {
  background: rgba(7,7,10,0.75);
  backdrop-filter: blur(20px) saturate(1.4);
  -webkit-backdrop-filter: blur(20px) saturate(1.4);
  border-bottom: 1px solid var(--border-subtle);
}
```

### Tag / Badge
```css
.tag {
  font-size: 11.5px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: var(--accent-bright);
}
.badge {
  font-size: 10.5px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  padding: 3px 8px;
  border-radius: 4px;
}
```

### Terminal / Code Block
```css
.terminal {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  font-family: var(--mono);
  font-size: 12.5px;
  line-height: 1.8;
  position: relative;
  overflow: hidden;
}
.terminal::before {
  content: '';
  position: absolute;
  top: 0; left: 0; right: 0;
  height: 2px;
  background: var(--shadow-grad);
}
/* Syntax colors */
.t-comment { color: var(--text-3); }
.t-cmd     { color: var(--green); }
.t-flag    { color: var(--accent-bright); }
.t-out     { color: var(--text-3); font-style: italic; }
.c-key     { color: var(--blue); }
.c-str     { color: var(--green); }
.c-bool    { color: var(--amber); }
```

### Section Divider
```css
.section-divider {
  height: 1px;
  background: linear-gradient(90deg,
    transparent 0%,
    var(--penumbra) 20%,
    rgba(139,124,240,0.12) 50%,
    var(--penumbra) 80%,
    transparent 100%
  );
}
```

### Hero Glow Background
```css
.hero::before {
  content: '';
  position: absolute;
  top: -200px; left: 50%;
  transform: translateX(-50%);
  width: 900px; height: 900px;
  background: radial-gradient(
    ellipse at 50% 30%,
    rgba(139,124,240,0.10) 0%,
    rgba(92,168,232,0.04) 30%,
    transparent 65%
  );
  pointer-events: none;
}
```

### Gradient Text
```css
.grad {
  background: var(--shadow-grad);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

### Stat Number
```css
.stat-number {
  font-size: 34px;
  font-weight: 800;
  letter-spacing: -0.03em;
  background: var(--shadow-grad);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
```

### Fleet Card (Device Node)
```css
.fleet-card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  padding: 24px 20px;
  text-align: center;
  position: relative;
  overflow: hidden;
}
.fleet-card::before {
  content: '';
  position: absolute;
  top: 0; left: 0; right: 0;
  height: 2px;
  background: var(--fc-color, var(--accent));
}
.fc-name { font-size: 16px; font-weight: 750; }
.fc-host { font-size: 11px; color: var(--text-3); font-family: var(--mono); }
.fc-role { font-size: 10.5px; font-weight: 700; text-transform: uppercase; letter-spacing: 0.08em; }
.fc-status { font-size: 11px; color: var(--text-3); display: flex; align-items: center; gap: 6px; }
.fc-status .dot { width: 5px; height: 5px; border-radius: 50%; background: var(--green); }
```

### Mesh Node (Visualization)
```css
.mesh-node-dot {
  width: 10px; height: 10px;
  border-radius: 50%;
  animation: meshPulse 3s ease infinite;
}
@keyframes meshPulse {
  0%,100% { box-shadow: 0 0 4px currentColor, 0 0 10px currentColor; }
  50% { box-shadow: 0 0 8px currentColor, 0 0 20px currentColor; }
}
.mesh-line {
  height: 1px;
  background: linear-gradient(90deg, var(--from), var(--to));
  opacity: 0.3;
}
```

## Animations
```css
/* Fade Up (scroll reveal) */
.fade-up {
  opacity: 0; transform: translateY(20px);
  transition: opacity 0.5s ease, transform 0.5s ease;
}
.fade-up.visible { opacity: 1; transform: translateY(0); }

/* Stagger children */
.fade-up:nth-child(2) { transition-delay: 0.08s; }
.fade-up:nth-child(3) { transition-delay: 0.16s; }
.fade-up:nth-child(4) { transition-delay: 0.24s; }

/* Cursor blink */
@keyframes cursorBlink { 0%,100%{opacity:1} 50%{opacity:0} }

/* Panel slide in */
@keyframes panelIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: none; } }
```

## Canvas Visualization Tokens (for node maps, force graphs)
```
Background:        radial-gradient from #0e0e13 center to #07070a edge
Grid:              rgba(35, 35, 48, 0.2) lines, 40px spacing
Node fill:         gradient from category color to darker shade
Node glow:         radial gradient halo using category color
Edge default:      rgba(100, 100, 130, 0.15)
Edge highlight:    rgba(139, 124, 240, 0.55)
Particle color:    source node's category color
Status dot green:  #4dd4a0
Status dot amber:  #f0a050
Status dot red:    #e86080
Tooltip bg:        rgba(14, 14, 19, 0.85) with blur(16px)
Tooltip border:    rgba(139, 124, 240, 0.18)
HUD bg:            rgba(14, 14, 19, 0.72) with blur(12px)
Font:              'JetBrains Mono', 'SF Mono', monospace
```

## Fleet Codenames (Canonical)
```
sd-mba-client    J.A.R.V.I.S    MacBook Air M4       Operator
sd-mini-core     F.R.I.D.A.Y    Mac Mini M2          Gateway
sd-opti-worker   A.U.R.I.O.N    OptiPlex Linux       Anchor
sd-x1-rover      U.L.T.R.O.N    ThinkPad X1          Rover
sd-iphone13-work iPhone 13       iOS 18               Mobile
sd-pixel10-edge  Pixel 10 Pro    Android              Edge
sd-pixel9-edge   Pixel 9 Pro     Android              Edge
```


## Pipeline

```
Intent → Resolve target → Execute operation → Verify result → Report
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Standard output | Normal use |
| `verbose` | Detailed diagnostics | Debugging |
| `json` | Machine-readable output | Programmatic use |

## Artifact Routing

- Reports: `ShadowArchive/80-reports/`
- Config changes: `system/controls/` or `config/system/`

## Prerequisites

- SHADOW infrastructure accessible
- Appropriate auth/SSH for mesh targets

## Contract

- Never modify production config without operator confirmation
- Report all changes with before/after diff
- Preserve existing configurations with backup

