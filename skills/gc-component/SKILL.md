---
name: GPT Codex Component
description: Use when generating uI components following GPT Codex design. Diff views, status indicators, approval overlays, command popups, shimmer text.
icon: icon.svg
metadata:
  depends_on: [design-system]
  category: design/ui
  family: design-system
  lifecycle: active
  canonical_slug: gc-component
  icon_style: craft-category-glyph-v1
---

Generate a UI component following gc-design-system tokens.

Available components:

- **diff-view**: Unified or split diff display
  - Addition lines: bg #213A2B (dark) / #dafbe1 (light), prefix "+"
  - Deletion lines: bg #4A221D (dark) / #ffebe9 (light), prefix "-"
  - Line number gutter: 2 columns wide (LIVE_PREFIX_COLS)
  - Light theme gutter fg: #1f2328
  - Light theme number bg: #aceebb (add) / #ffcecb (del)

- **status-indicator**: Animated status display
  - Spinner at 32ms frame rate
  - Header text in bold
  - Elapsed timer in dim (format: "0s" / "1m 00s" / "1h 02m 03s")
  - Up to 3 detail lines, each prefixed "  └ "
  - Details rendered in dim

- **approval-overlay**: Modal for confirming actions
  - Overlay backdrop with blur
  - Shows proposed changes as diff patches
  - Accept / Reject / Edit action buttons
  - Key hints in dim (e.g., "y to accept", "n to reject")

- **command-popup**: Floating command input
  - Text input with blinking cursor
  - History navigation with ↑ ↓
  - Dismiss with Escape
  - Dark bg #1a1a1a, border #333333, radius 8px

- **key-hint**: Keyboard shortcut display
  - Dim styled text
  - Platform-aware modifiers: ctrl (Linux/Win), ⌥ (macOS), shift
  - Arrow keys as unicode: ↑ ↓ ← →
  - Format: `[dim]shortcut description[/dim]`

- **chat-composer**: Multi-line text input
  - Auto-growing textarea
  - Paste burst handling (debounced paste detection)
  - Submit: Enter, Newline: Shift+Enter
  - Border focus glow: #10a37f

- **file-search**: Fuzzy file browser popup
  - Text input with live filtering
  - Scrollable results list
  - Enter to select, Escape to dismiss
  - Highlight matched characters in #10a37f

- **shimmer-text**: Loading placeholder text
  - Cosine-based sweep animation, 2s period
  - Band half-width: 5.0 units, blend intensity: 0.9
  - CSS gradient implementation for web context
  - Fallback: dim pulsing text

All components must:
1. Follow the gc-design-system token system exactly
2. Include hover, focus, and active states where applicable
3. Support prefers-reduced-motion (disable animations, use static fallbacks)
4. Use proper semantic HTML
5. Use accent #10a37f for interactive elements and focus rings

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

