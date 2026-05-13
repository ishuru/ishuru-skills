---
name: Browser Surface Bridge
description: "Invisible authenticated data access for Craft Agent. Four layers: (1) Cookie export → curl (Tier 2, zero browser). (2) Shared cookie jar via browser-auth-sync (Tier 2, zero focus theft). (3) Dispatched auth sessions. (4) AppleScript JS injection — Safari safe, Chrome BANNED without operator approval. All operations gated on active Tailscale tailnet. Trigger on 'outlook search', 'sharepoint search', 'M365 search', 'browser bridge', 'auth node', 'browser auth', 'cookie sync', 'authenticated scrape', 'login wall', or when the agent browser hits an auth wall."
icon: icon.svg
metadata:
  category: automation/browser
  family: automation
  lifecycle: active
  canonical_slug: browser-surface-bridge
  requires: [tailscale]
  security: tailnet-gated
  invisible_first: true
  tier: 2
---

# Browser Surface Bridge

## MANDATORY: Tailnet Gate

**Every operation in this skill requires an active Tailscale tailnet connection.** No exceptions.

```bash
tailgate && proceed || stop
```

If `tailgate` returns non-zero, the agent MUST NOT:
- Read browser cookie databases
- Execute `browser-auth-sync` (any command)
- Inject tokens into any partition
- Extract tokens to any output
- Dispatch to auth-node sessions for authenticated data

## Invisible-First Layer Priority

| Priority | Method | Focus Theft | Speed | When to Use |
|---|---|---|---|---|
| **1** | `export-cookies.py` → `curl` | ❌ None | ~2s | SharePoint/Outlook REST API queries |
| **2** | `browser-auth-sync` cookie injection | ❌ None | ~2s | When browser session needs auth |
| **3** | Safari `do JavaScript` | ❌ None | ~1s | Sites with Safari auth session |
| **4** | Dispatched auth session | ❌ None | ~5s | Interactive pages (click, fill) |
| **5** | Chrome `execute javascript` | ⚠️ **ALWAYS steals focus** | ~1s | **BANNED** without operator approval |

### Focus Theft Warning — Chrome AppleScript

**Every `tell application "Google Chrome"` AppleScript call steals focus — even read-only queries.** This was measured and confirmed:

| Pattern | Steals Focus? |
|---|---|
| `tell app "Chrome" to get title of active tab` | ✅ YES |
| `tell app "Chrome" to execute javascript "..."` | ✅ YES |
| `tell app "Safari" to do JavaScript "..."` | ❌ NO |

Never use Chrome AppleScript without explicit operator approval.

## Layer 1: Cookie Export → curl (Primary — Invisible)

Export Chrome cookies to Netscape cookie jar, then use `curl` for REST API access. Zero browser. Zero focus theft. ~2 seconds.

```bash
# Export SharePoint cookies (Profile 5 = Astemo M365)
python3 ~/.agents/skills/browser-surface-bridge/scripts/export-cookies.py \
  -p "Profile 5" -d sharepoint -o /tmp/sp-cookies.txt

# Query SharePoint REST API
curl -b /tmp/sp-cookies.txt \
  "https://astemogroup.sharepoint.com/sites/Hoe-Axle/_api/web/lists?\$select=Title,ItemCount&\$top=5" \
  -H "Accept: application/json;odata=nometadata"

# Export Outlook cookies
python3 ~/.agents/skills/browser-surface-bridge/scripts/export-cookies.py \
  -p "Profile 5" -d outlook -o /tmp/outlook-cookies.txt

# Export YouTube cookies
python3 ~/.agents/skills/browser-surface-bridge/scripts/export-cookies.py \
  -p Default -d youtube.com -o /tmp/yt-cookies.txt

# Clean up — these are decrypted auth tokens
rm -f /tmp/sp-cookies.txt /tmp/outlook-cookies.txt
```

### Cookie Export Security

- Output gets `chmod 600` — contains decrypted auth tokens
- Use `/tmp` (macOS ramdisk) — not persisted across reboots
- Delete immediately after use
- Cookie DB is read with `?mode=ro` — never modifies Chrome state

### How It Works

```
Chrome SQLite Cookies DB → Keychain "Chrome Safe Storage" key
  → PBKDF2 (SHA1, "saltysalt", 1003 iterations, 16-byte key)
  → AES-128-CBC (IV = 16 spaces)
  → Strip 32-byte hash prefix (Chrome meta_version >= 24)
  → Strip PKCS7 padding
  → Plaintext cookie value
  → Netscape cookie jar file
  → curl -b cookies.txt → REST API
```

### Proven Working

| Service | Profile | Cookies | Result |
|---|---|---|---|
| SharePoint REST API | Profile 5 | 29 | ✅ Site title, lists, documents |
| YouTube | Default | 29 | ✅ Auth tokens for yt-dlp |
| Outlook | Profile 5 | 18 | ✅ Auth cookies exported |

## Layer 2: Shared Cookie Jar (browser-auth-sync)

Copy plaintext auth tokens from the richest cookie store into any session's Chromium partition. Navigate to the target site → SSO auto-completes.

```
Source: Any Craft Agent partition or Chrome profile (v10 AES decrypt)
  ↓  browser-auth-sync sync
Target: Current session's partition
  ↓  browser_tool navigate
Result: Authenticated access — no login, no dispatch, no AppleScript
```

**Proven:** 109 tokens injected → navigated to SharePoint → SSO auto-completed → full authenticated access.

**Token chain (M365):**
```
ESTSAUTHPERSISTENT (90d, .login.microsoftonline.com) ← refreshes everything
  → SPOIDCRL (2d, per-domain) → FedAuth + rtFa (3d) → SPA_RT (5d)
```

## Layer 3: Dispatched Auth Sessions

Spawn a persistent session whose only job is holding an authenticated browser. Other sessions dispatch via `send_agent_message`.

```
Work session → send_agent_message → Auth node session → browser_tool → results back
```

## Layer 4: AppleScript JS Injection (Fallback)

### Safari — Focus-Safe (✅)

```bash
# Safari does NOT steal focus
osascript -e 'tell application "Safari" to do JavaScript "document.title" in document 1'
```

### Chrome — BANNED Without Operator Approval (⚠️)

```bash
# Chrome ALWAYS steals focus — only with operator approval
osascript -e 'tell application "Google Chrome" to execute active tab of front window javascript "document.title"'
# ↑ This WILL steal focus. Only use when operator explicitly approves.
```

## Files

```
~/.local/bin/tailgate                          — tailnet gate (69ms)
~/.local/bin/browser-auth-sync                 — cookie sync CLI (tailnet-gated)
.agents/skills/browser-surface-bridge/scripts/export-cookies.py  — Chrome cookie export → curl
```

## Security Model

```
┌─────────────────────────────────────────────────────────┐
│  TAILNET GATE (tailgate — 69ms)                         │
│  ├── Verify: tailscale status → BackendState=Running    │
│  ├── Verify: Self.Online=True                           │
│  └── If FAIL → no token access, no browser auth ops     │
├─────────────────────────────────────────────────────────┤
│  COOKIE EXPORT (export-cookies.py)                      │
│  ├── Reads Chrome SQLite (mode=ro)                      │
│  ├── Decrypts via Keychain + PBKDF2 + AES-128-CBC       │
│  ├── Strips Chrome v24+ 32-byte hash prefix             │
│  ├── Writes Netscape cookie jar (chmod 600)             │
│  └── Caller must delete output file after use            │
├─────────────────────────────────────────────────────────┤
│  TOKEN ACCESS (browser-auth-sync)                       │
│  ├── status/sync/extract/inject — all require tailgate  │
│  └── Cookie sync is local-only (same machine)           │
└─────────────────────────────────────────────────────────┘
```
