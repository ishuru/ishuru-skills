---
name: GPT Codex Design System
description: 'Use when the user needs: GPT Codex design system tokens and patterns. Use when building UI matching the OpenAI Codex aesthetic — terminal-adaptive, semantic color system, shimmer effects.'
icon: icon.svg
user-invocable: false
metadata:
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: gc-design-system
  icon_style: craft-category-glyph-v1
---

# GPT Codex Design System Reference

## Color Tokens — Terminal / TUI Context

GPT Codex uses a **semantic, terminal-adaptive** color system. It avoids hardcoded ANSI values and instead maps colors by role. The terminal's default foreground/background are preferred for most text.

```
Semantic Role        ANSI Color     Usage
─────────────────────────────────────────────────────────
user-input           Cyan           User-typed text, tips, selections, status indicators
success / add        Green          Success messages, diff additions
error / delete       Red            Errors, failures, diff deletions
brand / code         Magenta        Codex-branded content, code highlights
general              Default/Reset  Body text, preferred for most content
header               Bold           Section headings, emphasis
secondary            Dim            Hints, elapsed timers, de-emphasized text
loading              Shimmer        In-progress operations (animated)
```

### Colors to AVOID in terminal context
- ANSI black or white as foreground (breaks on inverted terminals)
- ANSI blue (poor legibility on dark backgrounds)
- ANSI yellow (poor legibility on light backgrounds)

## Color Tokens — Diff View

### Dark Theme
```
--diff-add-bg: #213A2B
--diff-del-bg: #4A221D
```

### Light Theme
```
--diff-add-bg: #dafbe1
--diff-add-number-bg: #aceebb
--diff-del-bg: #ffebe9
--diff-del-number-bg: #ffcecb
--diff-gutter-fg: #1f2328
```

## Color Tokens — Web / Product Page (OpenAI Brand)

### Dark Theme (Primary)
```
--bg-primary: #0a0a0a
--bg-secondary: #1a1a1a
--bg-tertiary: #111111
--fg-primary: #f5f5f5
--fg-secondary: #a0a0a0
--fg-tertiary: #888888
--border-primary: #333333
--border-secondary: #2a2a2a
--accent-green: #10a37f
--accent-green-hover: #0d8c6d
--accent-green-glow: rgba(16, 163, 127, 0.3)
--error: #e74c3c
--success: #10a37f
--warning: #f39c12
--muted: #888888
```

### Light Theme
```
--bg-primary: #ffffff
--bg-secondary: #f7f7f8
--bg-tertiary: #ececec
--fg-primary: #1a1a1a
--fg-secondary: #555555
--fg-tertiary: #888888
--border-primary: #d9d9d9
--border-secondary: #e5e5e5
--accent-green: #10a37f
--accent-green-hover: #0d8c6d
```

## Shimmer Effect Tokens

The shimmer is a cosine-based sweep used for loading/thinking states.

```
--shimmer-base: terminal default fg (fallback RGB 128, 128, 128)
--shimmer-highlight: terminal default bg (fallback RGB 255, 255, 255)
--shimmer-blend-intensity: 0.9
--shimmer-sweep-period: 2.0s
--shimmer-band-half-width: 5.0 units
--shimmer-falloff: cosine-based (cos(pi * clamped_distance / band_half_width))
```

In web context, use CSS gradient animation:
```css
@keyframes gc-shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}
.gc-shimmer {
  background: linear-gradient(
    90deg,
    var(--fg-tertiary) 25%,
    var(--fg-primary) 50%,
    var(--fg-tertiary) 75%
  );
  background-size: 200% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: gc-shimmer 2s ease-in-out infinite;
}
```

## Typography

### Web Context
```
--font-primary: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif
--font-mono: 'Sohne Mono', 'Courier New', Consolas, monospace
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

### Terminal Context
```
Font:       Terminal default (no override)
Headers:    Bold modifier
Secondary:  Dim modifier
Key hints:  Dim modifier, arrows as unicode glyphs
Arrow keys: up ↑  down ↓  left ←  right →
Modifiers:  ctrl / ⌥ (macOS) / shift
```

## Spacing

### Web Context
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

### Terminal Context
```
LIVE_PREFIX_COLS: 2           (left gutter width in columns)
FOOTER_INDENT_COLS: 2         (footer indent in columns)
Detail indent: "  └ "        (4-char indent with tree connector)
Max detail lines: 3           (status indicator detail limit)
```

## Border & Radius

```
--radius-sm: 4px
--radius-md: 8px
--radius-lg: 12px
--radius-xl: 16px
--radius-full: 9999px
--border-width: 1px
```

## Shadows

```
--shadow-sm: 0 2px 8px rgba(0, 0, 0, 0.15)
--shadow-md: 0 4px 24px rgba(0, 0, 0, 0.2)
--shadow-lg: 0 8px 40px rgba(0, 0, 0, 0.4)
--shadow-glow-green: 0 0 20px rgba(16, 163, 127, 0.3)
```

## Transitions

```
--transition-fast: 150ms ease
--transition-normal: 300ms ease
--transition-slow: 500ms ease
```

## Animation Tokens

### Shimmer Sweep
```
Period: 2.0 seconds
Falloff: cosine-based
Band half-width: 5.0 units
Blend intensity: 0.9
```

### Spinner / Status Indicator
```
Frame rate: 32ms per frame (~31 fps)
Format: [spinner] header text [elapsed]
Detail prefix: "  └ " (4-char indent)
Max detail lines: 3
Dim styling on secondary elements (details, elapsed)
```

### Elapsed Timer Format
```
< 60s:    "0s", "1s", ... "59s"
< 1h:     "1m 00s", "1m 01s", ...
>= 1h:    "1h 02m 03s"
```

## Component Patterns

### Diff View
- Two-tone background: additions use `--diff-add-bg`, deletions use `--diff-del-bg`
- Line numbers in gutter with `LIVE_PREFIX_COLS` (2 columns) left margin
- Gutter foreground `--diff-gutter-fg` in light theme
- Prefix `+` for additions, `-` for deletions
- Context lines use default background

### Status Indicator
- Spinner character animates at 32ms frame rate
- Header text in bold, elapsed in dim
- Up to 3 detail lines, each prefixed with `"  └ "`
- Details in dim style

### Key Hints
- Rendered in dim style
- Platform-aware modifier keys: `ctrl` (Linux/Windows), `⌥` (macOS), `shift`
- Arrow keys as unicode: ↑ ↓ ← →
- Format: `[dim]ctrl+r to retry[/dim]`

### Approval Overlay
- Modal overlay for user confirmation
- Shows proposed changes (file patches)
- Accept / Reject / Edit options
- Keyboard shortcuts displayed as key hints

### Command Popup
- Floating popup for command entry
- Text input with cursor
- History navigation with ↑ ↓
- Dismiss with Escape

### File Search Popup
- Fuzzy matching file browser
- Results list with scroll
- Enter to select, Escape to dismiss

### Chat Composer
- Multi-line text input
- Paste burst handling (debounced paste detection)
- Submit with Enter, newline with Shift+Enter

## CSS Template

When generating HTML in the GPT Codex style, always start with this base:

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
body {
  background: #0a0a0a;
  color: #f5f5f5;
  font-family: system-ui, -apple-system, sans-serif;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}
code, pre, .mono {
  font-family: 'Sohne Mono', 'Courier New', Consolas, monospace;
}
::selection { background: rgba(16, 163, 127, 0.3); }
:focus-visible { outline: 2px solid #10a37f; outline-offset: 2px; }
a { color: #10a37f; text-decoration: none; }
a:hover { color: #0d8c6d; }
```
