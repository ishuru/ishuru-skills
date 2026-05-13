---
name: Safari Applescript Js
description: Use when injecting JavaScript into Safari from the terminal or a shell script, automating web UI actions in Safari via AppleScript, or when osascript escaping causes syntax errors.
icon: icon.svg
metadata:
  category: automation/browser
  family: automation
  lifecycle: active
  canonical_slug: safari-applescript-js
  icon_style: craft-category-glyph-v1
---

# Safari AppleScript + JavaScript Injection

## Overview

Safari exposes JavaScript execution via AppleScript's `do JavaScript` command. The escaping problem (quotes inside quotes inside shell strings) makes inline osascript commands fragile — use a `python3` wrapper to build the AppleScript string and pass it to `osascript -e`.

## The Python3 Wrapper Pattern

Avoids all shell escaping issues:

```python
#!/usr/bin/env python3
import subprocess

js_code = """
document.querySelector('input[name="email"]').value = 'user@example.com';
"""

applescript = f'''
tell application "Safari"
    tell front document
        do JavaScript "{js_code.replace('"', '\\"').replace(chr(10), ' ')}"
    end tell
end tell
'''

subprocess.run(['osascript', '-e', applescript])
```

Or inline from bash by calling python3 -c:

```bash
python3 -c "
import subprocess
js = 'document.title'
script = '''tell application \"Safari\"\ntell front document\nreturn do JavaScript \"''' + js + '''\"\nend tell\nend tell'''
r = subprocess.run(['osascript', '-e', script], capture_output=True, text=True)
print(r.stdout.strip())
"
```

## Target a Specific Window by Name

```python
import subprocess

def safari_js(js: str, window_name_prefix: str = None) -> str:
    if window_name_prefix:
        target = f'first document whose name starts with "{window_name_prefix}"'
    else:
        target = 'front document'

    applescript = f'''
tell application "Safari"
    tell {target}
        return do JavaScript "{js.replace(chr(34), chr(92)+chr(34)).replace(chr(10), ' ')}"
    end tell
end tell
'''
    result = subprocess.run(['osascript', '-e', applescript],
                            capture_output=True, text=True)
    return result.stdout.strip()

# Usage
title = safari_js('document.title', window_name_prefix='Linear')
print(title)
```

## Open a URL in Safari

```bash
osascript -e 'tell application "Safari" to open location "https://example.com"'
```

Or bring an existing tab to front by URL match:

```python
applescript = '''
tell application "Safari"
    repeat with w in windows
        repeat with t in tabs of w
            if URL of t contains "linear.app" then
                set current tab of w to t
                activate
                return
            end if
        end repeat
    end repeat
end tell
'''
subprocess.run(['osascript', '-e', applescript])
```

## Get Page Source / Extract Data

```python
js = "JSON.stringify([...document.querySelectorAll('h1')].map(e => e.textContent))"
result = safari_js(js)
import json
headings = json.loads(result)
```

## Known Limitations

| Limitation | Notes |
|---|---|
| Safari profile switching | Cannot be automated — requires manual interaction or macOS Shortcuts |
| Cross-origin iframes | `do JavaScript` cannot reach cross-origin frames |
| Page not loaded | Script runs immediately; add `delay 1` in AppleScript or poll `document.readyState` |
| AppleScript `do JavaScript` disabled | Must enable: Safari → Preferences → Advanced → Show Develop menu → Allow JavaScript from Apple Events |
| Return value type | Only strings returned; `JSON.stringify` objects before returning |

## Enable JavaScript from Apple Events

One-time setup (required for `do JavaScript` to work):

```bash
# Enable Develop menu
defaults write com.apple.Safari IncludeDevelopMenu -bool true
# Then manually: Safari → Develop → Allow JavaScript from Apple Events
```

Or automate via AppleScript menu click (fragile, depends on menu bar position):

```bash
osascript -e 'tell application "Safari" to activate'
# Then use System Events to click menu item — not recommended; do it manually once
```

## Common Mistakes

| Mistake | Fix |
|---|---|
| Inline quotes in `-e` string | Use python3 wrapper; never inline-escape multi-level quotes |
| `do JavaScript "..."` with embedded `"` | Escape inner quotes as `\"` before passing to AppleScript |
| Newlines in JS string passed to AppleScript | Replace `\n` with ` ` (space) before embedding |
| Targeting wrong window | Use `whose name starts with "..."` to target by title prefix |
| Safari not in foreground | `activate` doesn't guarantee document is ready; add `delay 0.5` |


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

