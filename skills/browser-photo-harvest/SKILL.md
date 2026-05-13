---
name: Browser Photo Harvest
description: Use when saving image/photo tabs already open in Google Chrome into a named folder, especially iCloud Drive Pictures folders, and optionally closing matched tabs or all Chrome tabs afterward.
metadata:
  category: automation/browser
  family: automation
  lifecycle: active
  canonical_slug: browser-photo-harvest
  icon_style: craft-category-glyph-v1
---

# Browser Photo Harvest

## Use this for

- “Save all these opened photos/images” from Google Chrome.
- Saving image tabs into `iCloud Drive/Pictures/<folder>` such as `Artemis II`.
- Closing browser tabs after successful save.
- Repeating the same workflow safely without rewriting AppleScript/download logic.

Do **not** use for normal web pages that need article extraction; use web/fetch skills for those.

## Tool

Bundled script:

```bash
python3 /Users/sdluffy/.agents/skills/browser-photo-harvest/scripts/save_chrome_image_tabs.py --folder "Artemis II" --close all
```

What it does:

1. Lists Google Chrome tabs via AppleScript.
2. Filters direct image URLs (`jpg`, `png`, `webp`, `gif`, `tif`, etc.).
3. Downloads each unique image URL into `~/Library/Mobile Documents/com~apple~CloudDocs/Pictures/<folder>`.
4. Verifies downloaded files are non-tiny (>1 KB).
5. Optionally closes matched image tabs or all Chrome windows.

## Common commands

| Need | Command |
|---|---|
| Preview matched image tabs only | `python3 scripts/save_chrome_image_tabs.py --folder "Artemis II" --dry-run` |
| Save to iCloud Pictures folder, leave browser open | `python3 scripts/save_chrome_image_tabs.py --folder "Artemis II"` |
| Save, then close only saved image tabs | `python3 scripts/save_chrome_image_tabs.py --folder "Artemis II" --close matched` |
| Save, then close all Chrome tabs/windows | `python3 scripts/save_chrome_image_tabs.py --folder "Artemis II" --close all` |
| Save only tabs containing a keyword | `python3 scripts/save_chrome_image_tabs.py --folder "Artemis II" --keyword Artemis --close matched` |
| Save to an explicit folder | `python3 scripts/save_chrome_image_tabs.py --dest "$HOME/Pictures/Artemis II" --close all` |

When running from outside the skill directory, use the absolute script path:

```bash
python3 /Users/sdluffy/.agents/skills/browser-photo-harvest/scripts/save_chrome_image_tabs.py --folder "Artemis II" --close all
```

## Safety rules

- Prefer `--dry-run` first if the operator did not explicitly say “close tabs”.
- If the operator says “close all browser tabs,” use `--close all` only after downloads complete and verification passes.
- If mixed non-photo work may be open, use `--close matched` or ask before closing all.
- If destination wording says “iCloud pictures,” default to `~/Library/Mobile Documents/com~apple~CloudDocs/Pictures/<folder>` and create it if missing.
- Report: number of matched image tabs, saved count, destination path, verification result, and what was closed.

## Notes

- Chrome image tabs opened from direct image URLs are the supported primary path.
- For gallery pages or JavaScript-heavy sites, first extract the direct image URLs or open the images in tabs, then run this skill.


## Pipeline

```
Intent → Resolve target app/tab → Execute browser action → Extract result → Return structured output
```

1. Identify target (URL, app, tab, element)
2. Connect to browser via Playwright/browser_tool or AppleScript
3. Execute action (navigate, extract, interact, screenshot)
4. Return structured output

## Modes

| Mode | Output | When |
|------|--------|------|
| `extract` (default) | Data from browser | Read operations |
| `interact` | Perform browser action | Click, fill, automate |
| `debug` | DOM/state inspection | Troubleshooting browser apps |

## Fallback Chain

1. browser_tool (Craft Agents built-in)
2. Playwright MCP tools
3. AppleScript Chrome/Safari control
4. chrome-web-cli

## Prerequisites

- Browser with authenticated session
- Playwright MCP or browser_tool available

## Contract

- **Never enter credentials** — assume session exists
- **Never submit forms** without operator approval
- **Respect existing tabs** — do not close operator tabs without permission

