---
name: Omarchy Design System
description: 'Use when the user needs: Omarchy OS native design system using the current Catppuccin Mocha (Aether) theme. Use when building any UI, visualization, or HTML output that should feel native to the desktop environment.'
icon: icon.svg
user-invocable: false
metadata:
  category: design/ui
  family: design
  lifecycle: active
  canonical_slug: omarchy-design-system
  icon_style: craft-category-glyph-v1
---

# Omarchy Design System — Aether (Catppuccin Mocha)

## Color Tokens

### Base
```
--ctp-base: #1e1e2e
--ctp-mantle: #181825
--ctp-crust: #11111b
```

### Surface
```
--ctp-surface0: #313244
--ctp-surface1: #45475a
--ctp-surface2: #585b70
```

### Overlay
```
--ctp-overlay0: #6c7086
--ctp-overlay1: #7f849c
--ctp-overlay2: #9399b2
```

### Text
```
--ctp-text: #cdd6f4
--ctp-subtext0: #a6adc8
--ctp-subtext1: #bac2de
```

### Accent Colors
```
--ctp-blue: #89b4fa      (primary accent)
--ctp-lavender: #b4befe
--ctp-sapphire: #74c7ec
--ctp-sky: #89dceb
--ctp-teal: #94e2d5
--ctp-green: #a6e3a1
--ctp-yellow: #f9e2af
--ctp-peach: #fab387
--ctp-maroon: #eba0ac
--ctp-red: #f38ba8
--ctp-mauve: #cba6f7
--ctp-pink: #f5c2e7
--ctp-flamingo: #f2cdcd
--ctp-rosewater: #f5e0dc
```

### ShadowSkills Category Mapping
```
Diagnostics: --ctp-sky (#89dceb)
Configuration: --ctp-mauve (#cba6f7)
System: --ctp-green (#a6e3a1)
Mesh: --ctp-peach (#fab387)
```

### Semantic
```
--accent: var(--ctp-blue)
--active-border: var(--ctp-mauve)
--success: var(--ctp-green)
--warning: var(--ctp-yellow)
--error: var(--ctp-red)
--info: var(--ctp-sky)
```

## Typography
```
--font-primary: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif
--font-mono: 'JetBrains Mono', 'Courier New', Consolas, monospace
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
--line-height: 1.6
```

## Spacing
```
4 / 8 / 12 / 16 / 20 / 24 / 32 / 40 / 48 / 64px
```

## Border & Radius
```
--radius-sm: 4px
--radius-md: 8px
--radius-lg: 12px
--radius-xl: 16px
--radius-full: 9999px
--border-width: 1px (2px for active/focus)
--border-color: var(--ctp-surface1)
--border-active: var(--ctp-blue)
```

## Shadows
```
--shadow-sm: 0 2px 8px rgba(0,0,0,0.2)
--shadow-md: 0 4px 16px rgba(0,0,0,0.25)
--shadow-lg: 0 8px 32px rgba(0,0,0,0.35)
--shadow-glow-blue: 0 0 16px rgba(137,180,250,0.25)
--shadow-glow-mauve: 0 0 16px rgba(203,166,247,0.25)
--shadow-glow-green: 0 0 16px rgba(166,227,161,0.25)
--shadow-glow-peach: 0 0 16px rgba(250,179,135,0.25)
```

## Transitions
```
--transition-fast: 150ms ease
--transition-normal: 250ms ease
--transition-slow: 400ms ease
```

## Component Patterns

### Cards
- Background: var(--ctp-surface0) (#313244)
- Border: 1px solid var(--ctp-surface1) (#45475a)
- Radius: var(--radius-lg) (12px)
- Padding: 16-24px
- Hover: border-color var(--ctp-blue), shadow-glow-blue
- Backdrop filter: blur(12px) for glassmorphism variant

### Buttons
- Primary: bg var(--ctp-blue), text var(--ctp-base)
- Secondary: bg var(--ctp-surface1), text var(--ctp-text)
- Ghost: bg transparent, text var(--ctp-subtext0), border var(--ctp-surface1)
- Hover: lighten bg 10%, add subtle glow
- Focus: outline 2px solid var(--ctp-blue), offset 2px

### Inputs
- Background: var(--ctp-surface0)
- Border: 1px solid var(--ctp-surface1)
- Focus: border var(--ctp-blue), shadow-glow-blue
- Placeholder: var(--ctp-overlay0)
- Radius: var(--radius-md)

### Tooltips
- Background: var(--ctp-surface0) with 0.94 alpha
- Border: 1px solid var(--ctp-surface1)
- Radius: var(--radius-lg)
- Backdrop: blur(16px)
- Shadow: var(--shadow-lg)
- Max-width: 320px

### Notifications (Mako)
- Background: #1e1e2eee
- Border: 2px solid var(--ctp-blue) for ShadowSkills
- Width: 420px, padding: 10px
- Font: Liberation Sans 11

### Walker (App Launcher)
- Uses Omarchy default theme at ~/.local/share/omarchy/default/walker/themes/
- Config at ~/.config/walker/config.toml

### Waybar
- Background: var(--ctp-base)
- Text: var(--ctp-text)
- Height: 26px, position: top

## CSS Base Template
```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
body {
  background: #1e1e2e;
  color: #cdd6f4;
  font-family: system-ui, -apple-system, sans-serif;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}
::selection { background: rgba(137,180,250,0.3); color: #cdd6f4; }
:focus-visible { outline: 2px solid #89b4fa; outline-offset: 2px; }
a { color: #89b4fa; text-decoration: none; }
a:hover { text-decoration: underline; }
code { font-family: 'JetBrains Mono', monospace; background: #313244; padding: 2px 6px; border-radius: 4px; }
```

## Desktop Integration Points
- Omarchy menu extension: ~/.config/omarchy/extensions/menu.sh
- Hyprland bindings: ~/.config/hypr/bindings.conf
- Waybar modules: ~/.config/waybar/config.jsonc
- Walker entries: ~/.local/share/applications/*.desktop
- Mako notifications: ~/.config/mako/config
- CLI launcher: ~/.local/bin/shadowskills
- Autostart: ~/.config/hypr/autostart.conf


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

