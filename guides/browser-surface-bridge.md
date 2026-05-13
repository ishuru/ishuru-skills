# browser-surface-bridge — Guide

## Quick Start

```
browser bridge outlook search "QBR meeting"
browser bridge sharepoint list "FH4S drawings"
```

## Auth Layers

| Layer | Method | Browser Required |
|-------|--------|-----------------|
| 1 | Cookie export → curl | No |
| 2 | Shared cookie jar (browser-auth-sync) | No |
| 3 | Dispatched auth sessions | Background |
| 4 | AppleScript JS injection | Yes (Safari) |

## Security

- **All operations gated on active Tailscale tailnet**
- Chrome JS injection **banned** without explicit operator approval
- Safari JS injection is the approved path for Layer 4

## Supported Services

- Outlook / M365 mail
- SharePoint / OneDrive
- M365 search (unified)

## Fallback

When the agent browser hits an auth wall, this skill auto-detects and routes to the appropriate auth layer.
