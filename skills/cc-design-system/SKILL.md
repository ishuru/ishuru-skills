---
name: Codex Design System
description: 'Use when the user needs: Codex official design system tokens and patterns. Use when building any UI, visualization, or HTML output to maintain consistency with the Codex brand.'
icon: icon.svg
user-invocable: false
metadata:
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: cc-design-system
  icon_style: craft-category-glyph-v1
---

# Codex Design System Reference

## Color Tokens (Dark Theme - Primary)

```
--bg-primary: #0a0a0a
--bg-secondary: #121212
--bg-tertiary: #1a1a1a
--bg-elevated: #252542
--fg-primary: #f5f5f5
--fg-secondary: #a0a0a0
--fg-tertiary: #808080
--border-primary: #555555
--border-secondary: #4d4d4d
--accent-orange: #C46849
--accent-cyan: #00e5ff
--accent-purple: #bb86fc
--accent-green: #69f0ae
--accent-amber: #ff9100
--error: #df6666
--success: #69f0ae
--warning: #ff9100
```

## Typography

```
--font-primary: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif
--font-mono: 'Courier New', 'Consolas', 'JetBrains Mono', monospace
--font-size-xs: 11px
--font-size-sm: 13px
--font-size-md: 14px
--font-size-lg: 16px
--font-size-xl: 18px
--font-size-2xl: 24px
--font-size-3xl: 28px
--font-weight-normal: 400
--font-weight-medium: 500
--font-weight-semibold: 600
--font-weight-bold: 700
--line-height-tight: 1.3
--line-height-normal: 1.5
--line-height-relaxed: 1.6
```

## Spacing

```
--space-1: 4px
--space-2: 8px
--space-3: 12px
--space-4: 16px
--space-5: 20px
--space-6: 24px
--space-8: 32px
--space-10: 40px
--space-12: 48px
--space-16: 64px
```

## Border & Radius

```
--radius-sm: 4px
--radius-md: 8px
--radius-lg: 10px
--radius-xl: 16px
--radius-full: 9999px
--border-width: 1px
```

## Shadows

```
--shadow-sm: 0 2px 8px rgba(0,0,0,0.15)
--shadow-md: 0 4px 24px rgba(0,0,0,0.2)
--shadow-lg: 0 8px 40px rgba(0,0,0,0.4)
--shadow-glow-cyan: 0 0 20px rgba(0,229,255,0.3)
--shadow-glow-purple: 0 0 20px rgba(187,134,252,0.3)
--shadow-glow-green: 0 0 20px rgba(105,240,174,0.3)
--shadow-glow-orange: 0 0 20px rgba(255,145,0,0.3)
```

## Transitions

```
--transition-fast: 180ms ease
--transition-normal: 300ms ease
--transition-slow: 500ms ease
--transition-menu-open: 600ms cubic-bezier(0.16, 1, 0.3, 1)
--transition-menu-close: 300ms cubic-bezier(0.16, 1, 0.3, 1)
```

## Component Patterns

### Buttons

- **Primary**: bg `#f5f5f5`, text `#0a0a0a`, border transparent, hover icon color change
- **Secondary**: bg `#555555`, text `#f5f5f5`, border visible
- **Tertiary**: bg `#0a0a0a`, text `#f5f5f5`, border secondary
- **All**: 300ms color transition, focus outline 2px offset 2px

### Cards/Panels

- Background: `--bg-secondary` or `--bg-tertiary`
- Border: `1px solid --border-secondary`
- Radius: `--radius-lg` (10px)
- Padding: `--space-4` to `--space-6`
- Shadow: `--shadow-md`
- Backdrop filter: `blur(12-16px)`

### Tooltips

- Max-width: 320px
- Padding: 18px 22px
- Radius: `--radius-lg`
- Shadow: `--shadow-lg`
- Animation: scale 0.95 to 1, opacity 0 to 1, 180ms
- Backdrop filter: `blur(16px)`

### Code Blocks

- Font: `--font-mono`
- Background: `--bg-tertiary`
- Border: `1px solid --border-secondary`
- Radius: `--radius-md`
- Padding: `--space-3 --space-4`
- Scrollbar: hidden

### Navigation

- Desktop: dropdown with 600ms open, 300ms close
- Hover: non-hovered items fade to `--fg-tertiary`
- Mobile: hamburger with 45 degree rotation, clip-path animation

### Status Indicators

- Rotating glyphs: `◈ ◓ ✽ ◐ ◑ ◒ ● ◯ ◇ ◆ ▲ ▼`
- 1000ms cycle interval
- Character typing: 100ms per char

### Focus States

- Outline: 2px solid currentColor
- Offset: 2px
- Links in buttons: z-index 4

### Responsive Breakpoints

- **xl**: >= 62em (desktop)
- **lg**: 48em - 62em (tablet)
- **md**: 30em - 48em (mobile landscape)
- **sm**: < 30em (mobile portrait)

## CSS Template

When generating HTML, always start with this base:

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
body {
  background: #0a0a0a;
  color: #f5f5f5;
  font-family: system-ui, -apple-system, sans-serif;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}
::selection { background: rgba(187,134,252,0.3); }
:focus-visible { outline: 2px solid currentColor; outline-offset: 2px; }
```


## Pipeline

```
Intent → Resolve target → Execute → Validate → Report
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Standard output | Normal use |
| `verbose` | Detailed diagnostics | Debugging |

## Artifact Routing

- Reports: `ShadowArchive/80-reports/`

## Contract

- Destructive operations require explicit operator confirmation
- Always dry-run before applying changes

