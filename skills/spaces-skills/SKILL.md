---
name: Spaces Skills
description: Use when managing iTerm2 dynamic profiles aligned with the ShadowSpaces framework. List, add, update, or remove terminal profiles for Spaces contexts and mesh SSH targets. Use when the user wants to manage iTerm profiles, add SSH profiles, change profile settings, or align terminal profiles with Spaces.
icon: icon.svg
metadata:
  category: mesh/shadow
  family: mesh
  lifecycle: alias
  canonical_slug: spaces-skills
  icon_style: craft-category-glyph-v1
  aliases:
  - /spaces-skills
  - spaces skills
  - spaces-skills
---

Manage iTerm2 dynamic profiles for the ShadowSpaces + Fleet terminal strategy.

## Reference Files

- Profiles: `~/Library/Application Support/iTerm2/DynamicProfiles/ShadowPair.json`
- Docs: `~/Spaces/Private/_infra/iterm/README.md`
- Naming: `~/Spaces/Private/_infra/iterm/NAMING.md`
- Theme: ShadowEmber (warm dark — #110a06 bg, #f4d9b6 fg, #ffb346 cursor)

## Fleet Identity

### Mac Cluster
| Codename | Hardware       | Agent   | SSH Alias          |
|----------|----------------|---------|--------------------|
| JARVIS   | MacBook Air M4 | sdluffy | — (this machine)   |
| FRIDAY   | Mac Mini M2    | leo     | friday             |

### Linux Cluster
| Codename | Hardware            | Agent  | SSH Alias |
|----------|---------------------|--------|-----------|
| AURION   | OptiPlex 9020 SFF   | leo    | aurion    |
| ULTRON   | ThinkPad X1 Carbon  | ishuru | x-1-lan   |

## Profile Categories

### Spaces (local contexts on JARVIS)
- `Custom Directory` + `Working Directory` anchored to the relevant workspace
- `Initial Text` bootstraps the shell and then gets out of the way
- Tags describe zone and use-case, not a rigid launcher role
- Current local space names:
  - `shadow-unos`
  - `shadow-duos`
  - `shadow-tres`

### Fleet (SSH to mesh devices)
- Prefer `Initial Text: ssh -t <alias> "... exec <shell> -l"` for shell-first remote workspaces
- `Bound Hosts` for auto profile switching on SSH
- Badge text should be explicit identity, e.g. `leo/FRIDAY`
- Tags: `["mesh", "<cluster>-cluster", ...]`
- Remote shell integration still happens after SSH connects

## Current Profiles

| Name | Type | Badge | Key | Target |
|------|------|-------|-----|--------|
| `shadow-unos` | local | `UNO` | `S` | local JARVIS operator shell |
| `shadow-duos` | local | `DUOS` | `L` | local JARVIS review shell |
| `shadow-tres` | local | `TRES` | `A` | local Astemo shell |
| `JARVIS` | local | `sdluffy/JARVIS` | `J` | local shell |
| `FRIDAY` | mesh | `leo/FRIDAY` | `F` | `ssh friday` |
| `AURION` | mesh | `leo/AURION` | `R` | `ssh aurion` |
| `ULTRON` | mesh | `ishuru/ULTRON` | `U` | `ssh ultron` |

## Badge Color Palette (permanent assignments)

| Identity     | Hex       | RGB            |
|--------------|-----------|----------------|
| Home/JARVIS  | #b34922   | 179, 73, 34    |
| Professional | #8c5926   | 140, 89, 38    |
| Private      | #737373   | 115, 115, 115  |
| Personal     | #4073bf   | 64, 115, 191   |
| FRIDAY       | #3399b3   | 51, 153, 179   |
| ULTRON       | #804db3   | 128, 77, 179   |
| AURION       | #4da64d   | 77, 166, 77    |
| VPS/Cloud    | #b38f4d   | 179, 143, 77   |
| VISION       | #b34040   | 179, 64, 64    |

## Operations

### Adding a new profile
1. Read `ShadowPair.json`
2. Deep-copy the nearest matching profile as template
3. Generate new UUID4 for `Guid`
4. Apply category-specific fields (spaces vs fleet)
5. Set badge color from palette (or assign new if new identity)
6. Assign unique single-letter `Shortcut` (L/I/A/B/F/U/R taken)
7. Write back to `ShadowPair.json`
8. Update README.md

### Updating a profile
1. Read `ShadowPair.json` → find by Name → update fields → write back

### Listing
1. Read `ShadowPair.json` → show table: Name | Badge | Shortcut | Type | Target | Color

### Removing
1. Read `ShadowPair.json` → remove by Name → write back → update README.md

## Naming Rules
- Codenames: UPPERCASE formal, lowercase identifiers
- Prefer explicit `user/DEVICE` badges for mesh profiles
- Agent names = OS usernames (used for SSH, paths)
- Profile names: lowercase and compact for local spaces, uppercase for device identities when that is the canonical device name
- Spaces: Capitalized in paths, lowercase in env vars
- Never duplicate GUIDs or shortcuts
- Current host automation layer may also use `it2` when available

$ARGUMENTS
