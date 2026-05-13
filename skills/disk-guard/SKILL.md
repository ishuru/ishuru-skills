---
name: Disk Guard
description: Use when disk space is low, apps crash due to full disk, WiFi stops working, Tailscale disconnects, or any "no space left on device" symptom. Also trigger on "disk full", "out of space", "reclaim space", "free up disk", "disk emergency", "storage full", "wifi not working because disk full".
icon: icon.svg
metadata:
  category: mesh/shadow
  family: mesh
  lifecycle: active
  canonical_slug: disk-guard
  icon_style: craft-category-glyph-v1
---

# Disk Guard — Comprehensive Disk Pressure Management

## Active Daemon

`com.shadow.mole-agent` — Python daemon, every 5 minutes, launchd managed.

### Dynamic Threshold (ComfyUI-inspired VRAM model)

```
threshold = (10GB + consumption_rate × interval × safety) × session_mult
           + memory_pressure_headroom + spiral_risk_headroom
```

- **Idle, no sessions**: ~10GB
- **Heavy agent work (100MB/min)**: ~13GB
- **8+ sessions**: multiplier scales to 2.6×
- **Memory compressor active**: +2GB headroom (swap may follow)
- **Swap files detected**: +1.5GB per swap file
- **Death spiral detected**: +3GB preemptive bump

### Memory-Disk Death Spiral Detection

```
Many sessions → RAM fills → compressor activates → swap allocated
  → disk fills → processes crash → crash logs → more disk
    → network dies → API fails → everything dead
```

Detection: compressor pages > 100K OR swap files present AND consumption rate > 20MB/min.
Preempts threshold bump even when usage % is still healthy.

### Alert Tiers & Workflows

| Usage | Spiral? | Workflow | Risk |
|-------|---------|----------|------|
| 75-85% | No | **observation** (read-only) | None |
| 75-85% | No | **caution** (dry-run analysis) | None |
| 75-85% | Yes | **standard** (live reclaim) | Low |
| 85-95% | Any | **deep** (full reclaim) | Medium |
| >95% | Any | **emergency** (everything) | High |
| Any | 3+ net fails | **emergency** (forced) | High |

### Complete Tool Registry (16 tools)

**Tier 0 — Observation**
| Tool | What | Risk |
|------|------|------|
| `mem_pressure_read` | RAM usage, compressor pages, swap outs | None |

**Tier 1 — Safe (no data loss)**
| Tool | What | Reclaimable | Risk |
|------|------|------------|------|
| `trash` | Empty ~/.Trash | 0-19GB | None |
| `system_caches` | Homebrew, pip, Xcode caches | 0-100MB | None |
| `temp_files` | /tmp/shadow-*, /tmp/*.log (1d+) | Variable | None |
| `crash_logs` | DiagnosticReports (7d+) | 0-50MB | None |
| `mole_clean` | Mole CLI cache cleanup | Variable | None |
| `mole_installer` | Old DMG/PKG installers | 0-200MB | None |

**Tier 2 — Low risk**
| Tool | What | Reclaimable | Risk |
|------|------|------------|------|
| `opencode_logs` | Truncate >50MB logs, delete 1d+ | 0-600MB | Low |
| `agent_caches` | Claude screenshots, Cursor ext (3d+) | 0-100MB | Low |
| `mole_purge` | Old node_modules, build artifacts | 0-500MB | Low |
| `hermes_cleanup` | Hermes old sessions (7d+) | 0-500MB | Low |
| `sleep_image_disable` | Disable hibernation sleep image | 2.0GB | Low (needs sudo) |

**Tier 3 — Medium (reclaimable, may break something)**
| Tool | What | Reclaimable | Risk |
|------|------|------------|------|
| `git_gc` | ~.git aggressive gc (12GB loose objects!) | 0-10GB | Medium |
| `platformio_cleanup` | PlatformIO build cache | 0-600MB | Medium |
| `colima_cleanup` | Docker VM disk image | 0-150MB | Medium |
| `codex_cleanup` | Old worktrees, backups, .tmp | 0-1GB | Medium |
| `mole_optimize` | Bluetooth refresh, Spotlight, permissions | N/A | Medium |

**Tier 4 — High (system-level)**
| Tool | What | Reclaimable | Risk |
|------|------|------------|------|
| `purge_swap` | Delete swap files | Variable | High (needs sudo) |

### Hidden Consumers (detected but not auto-deleted)

| Source | Size | Notes |
|--------|------|-------|
| `~/.git/objects` | **12GB** | Bare repo, 83K loose objects, no packs |
| `~/.platformio/` | **2.9GB** | Build cache + packages |
| `~/.codex/` | **2.3GB** | Sessions, worktrees, backups |
| `/private/var/vm/sleepimage` | **2.0GB** | Hibernation, reclaimable |
| `~/.colima/` | **1.1GB** | Docker VM disk image |
| `~/.hermes/` | **706MB** | Ambient agent data |
| `~/Library/Caches/com.apple.softwareupdate` | **~7GB** | Pending macOS updates |
| Pending macOS updates | **~7GB** | Auto-downloaded, not yet applied |

### Metrics Log

Every check writes to `/tmp/shadow-mole-agent-metrics.jsonl`:
- free_gb, consumption_rate, memory pressure level, compressed pages
- swap_file_count, active sessions, hidden_consumers_gb, health_score
- action taken, tools run, bytes freed

### Manual Override

```bash
# Check state
cat /tmp/shadow-mole-agent-state.json

# Inject net failure count (after you see API failures)
python3 -c "
import json; s=json.load(open('/tmp/shadow-mole-agent-state.json'))
s['consecutive_net_failures']=3
json.dump(s,open('/tmp/shadow-mole-agent-state.json','w'))
"

# Run manual cycle
python3 ~/.config/shadow/mole-agent/mole-agent.py

# Run specific tool
python3 -c "
from importlib import import_module
m = import_module('mole-agent')
r = m.tool_git_gc()
print(f'{r.tool}: {r.message} ({r.freed_gb:.1f}GB)')
"

# Run observation only
python3 -c "
from importlib import import_module
m = import_module('mole-agent')
r = m.run_tool('mem_pressure_read')
print(r.message)
"

## The Killers (JARVIS MBA, ordered by size)

| Source | Size | Notes |
|--------|------|-------|
| Trash | up to 19GB | Silently accumulates |
| OpenCode logs | 600MB+ per session | `~/.local/share/opencode/log/` |
| Google (Chrome + Drive) | 5.3GB | App Support |
| Xcode | 4.8GB | If not actively developing |
| MS Office (Excel + PPT) | 3.7GB | Only need on work laptop |
| Claude data | 462MB+ | `~/Library/Application Support/Claude` |
| Cursor | 608MB | `~/Library/Application Support/Cursor` |
| Codex | 2.3GB | `~/.codex/` |
| Dropbox | 1.1GB | App Support |
| Syncthing | 512MB | App Support |

## Emergency Recovery Protocol

Run these in order. Each step is safe.

### Step 1: Instant wins (no risk)

```bash
# Empty trash (often 10-19GB)
rm -rf ~/.Trash/*

# Clear system caches (safe, rebuilds on next use)
rm -rf ~/Library/Caches/Homebrew/*
rm -rf ~/Library/Caches/com.apple.dt.Xcode/*
rm -rf ~/Library/Caches/pip/*
rm -rf ~/Library/Caches/yarn/*
rm -rf ~/Library/Caches/npm/_cacache/tmp/*

# Clear macOS update caches
sudo rm -rf /Library/Caches/com.apple.softwareupdate/*
```

### Step 2: OpenCode log rotation (the silent killer)

```bash
# Check current log size
du -sh ~/.local/share/opencode/log/

# Delete logs older than today (they're debug logs, not user data)
find ~/.local/share/opencode/log/ -name "*.log" -mtime +1 -delete

# Truncate today's log if over 50MB
find ~/.local/share/opencode/log/ -name "*.log" -size +50M -exec truncate -s 0 {} \;

# Also check Codex logs
du -sh ~/.codex/logs/ 2>/dev/null
find ~/.codex/logs/ -name "*.log" -mtime +1 -delete 2>/dev/null
```

### Step 3: Agent tool caches

```bash
# Claude conversation cache
rm -rf ~/Library/Application\ Support/Claude/projects/*/conversations/*/cache/*

# Cursor extensions cache
rm -rf ~/.cursor/extensions/*/node_modules/.cache/

# Codex temp files
rm -rf ~/.codex/tmp/* 2>/dev/null

# agent-browser screenshots (can grow huge)
du -sh ~/.agent-browser/screenshots/ 2>/dev/null
find ~/.agent-browser/screenshots/ -mtime +3 -delete 2>/dev/null
```

### Step 4: Use Mole for deeper cleanup

```bash
# Preview what Mole would clean
mo clean --dry-run

# Actually clean
mo clean

# Purge old project artifacts (node_modules, .gradle, etc.)
mo purge --dry-run
mo purge

# Remove old installer files (DMGs, PKGs)
mo installer --dry-run
mo installer

# Find big files interactively
mo analyze
```

### Step 5: Offload infrequently used apps to external drive

```bash
# Create app staging on external drive
EXTERNAL="/Volumes/☯Duality/Applications-Offloaded"
mkdir -p "$EXTERNAL"

# Move heavy apps (adjust list as needed)
# Use symlinks so launchers still find them
for app in "Xcode" "Microsoft Excel" "Microsoft PowerPoint" "LM Studio" "Conductor"; do
  if [ -d "/Applications/${app}.app" ]; then
    mv "/Applications/${app}.app" "$EXTERNAL/"
    ln -s "$EXTERNAL/${app}.app" "/Applications/${app}.app"
    echo "Offloaded: ${app}"
  fi
done
```

## Prevention: LaunchAgent for Log Rotation

Create `~/Library/LaunchAgents/com.shadow.log-rotate.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.shadow.log-rotate</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>
        find ~/.local/share/opencode/log/ -name "*.log" -size +100M -exec truncate -s 0 {} \;
        find ~/.local/share/opencode/log/ -name "*.log" -mtime +3 -delete
        find ~/.codex/logs/ -name "*.log" -mtime +3 -delete 2>/dev/null
        du -sh ~/.local/share/opencode/log/ >> /tmp/shadow-log-rotate.log 2>&1
        </string>
    </array>
    <key>StartInterval</key>
    <integer>3600</integer>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

Install: `launchctl load ~/Library/LaunchAgents/com.shadow.log-rotate.plist`

## Prevention: Disk Space Monitor

Check disk space at session start and warn when above thresholds:

```bash
# Get percent used (macOS df -h)
pct=$(df / | awk 'NR==2{print $5}' | tr -d '%')
if [ "$pct" -gt 85 ]; then
  echo "WARNING: Disk ${pct}% full. Run emergency recovery."
elif [ "$pct" -gt 75 ]; then
  echo "CAUTION: Disk ${pct}% full. Consider cleanup."
fi
```

## Critical Thresholds for JARVIS (250GB SSD)

| Usage | GB Free | Action |
|-------|---------|--------|
| < 70% | > 75GB | Healthy — no action |
| 70-85% | 37-75GB | Caution — run `mo clean`, check Trash |
| 85-95% | 12-37GB | Warning — run full emergency protocol |
| > 95% | < 12GB | Critical — everything breaks, act now |

## Common Mistakes

- **Assuming apps handle their own logs.** They don't. OpenCode grew to 622MB in one session.
- **Forgetting Trash.** macOS doesn't auto-empty. 19GB can sit there silently.
- **Running multiple OpenCode sessions.** Each generates its own log. 4 sessions = 4x log growth.
- **Only clearing caches.** Caches are small. The real killers are logs, Trash, and app data.

## Pipeline

```
Input (trigger: disk full, out of space, reclaim, emergency, or auto from mole-agent daemon)
  ↓
Read Current State (df -h, mem_pressure, swap, consumption rate)
  ↓
Classify Tier (75-85% observation → 85-95% deep → >95% emergency)
  ↓
Spiral Detection (compressor pages, swap files, net failures)
  ↓
Select Tools by Tier (Tier 0–4 registry, safe-first ordering)
  ↓
Execute Reclaim (trash → caches → logs → agent data → git gc → swap)
  ↓
Verify (df -h post-reclaim, confirm services recovered)
  ↓
Artifact Generation
  ├── /tmp/shadow-mole-agent-metrics.jsonl  (every check)
  ├── /tmp/shadow-mole-agent-state.json     (current state)
  └── ShadowArchive/80-reports/disk-reclaim-YYYY-MM-DD.md (session report)
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Auto-classify tier and run appropriate reclaim tools | Any disk pressure trigger |
| `status` | Current disk state: free GB, tier, consumption rate, spiral risk | `disk status`, quick check |
| `scan` | Identify hidden consumers without reclaiming | `disk scan`, sizing question |
| `emergency` | Run full emergency recovery protocol immediately | `disk emergency`, >95% usage |
| `caution` | Run Tier 0–1 tools only (safe, no data loss) | `disk caution`, preventative |
| `deep` | Run Tier 0–3 tools (includes git gc, agent caches) | `disk deep`, 85-95% usage |

## Artifact Routing

| Artifact | Path | Purpose |
|----------|------|----------|
| Metrics log | `/tmp/shadow-mole-agent-metrics.jsonl` | Every check, continuous data |
| State snapshot | `/tmp/shadow-mole-agent-state.json` | Current disk/mem state |
| Reclaim report | `ShadowArchive/80-reports/disk-reclaim-YYYY-MM-DD.md` | What was reclaimed, how much |

## Fallback Chain

1. **Primary:** mole-agent daemon (auto) or manual tool execution via Tier 0–4 registry
2. **mole-agent not running:** Run tools manually via bash commands in this skill
3. **Mole CLI (`mo`) not available:** Execute equivalent bash commands directly from the Emergency Recovery Protocol
4. **sudo required (Tier 3–4):** Report which tools need sudo; ask operator; do not auto-escalate
5. **Disk too full to write metrics:** Write to `/tmp` (RAM-backed); if even that fails, output to stdout only
6. **Last resort:** Manual recovery steps printed to terminal for operator execution

## Prerequisites

- macOS with standard unix tools (`df`, `du`, `rm`, `find`)
- `mole-agent` daemon installed and running (optional, for auto mode)
- `mo` CLI available (optional, for `mo clean/purge/installer` commands)
- `cliclick` for GUI-free mouse automation (optional)
- sudo access for Tier 3–4 tools (sleep image, swap purge) — only when explicitly approved
- External volume mounted for app offloading (optional)

## Error Handling

| Failure | Recovery |
|---------|----------|
| Cannot empty Trash (permission) | `rm -rf ~/.Trash/*` with fallback; report if still fails |
| `find` times out on large tree | Sample top-level dirs; estimate sizes; proceed with known large targets |
| git gc fails (bare repo corruption) | Skip; report; do not force gc on corrupted repos |
| `truncate` not available | Use `> file` redirection instead; same effect |
| Disk still critical after full reclaim | Report; suggest app offload to external volume; suggest deleting large unused apps |
| Services not recovering after reclaim | Restart Tailscale (`tailscale up`); restart network (`sudo dscacheutil -flushcache`); report |
| External volume not mounted for offload | Skip offload step; continue with local-only reclaim |

## Contract

- **Safe-first ordering.** Always run Tier 0–1 before Tier 2–4. Never skip to high-risk tools when safe tools might suffice.
- **No auto-sudo.** Tier 3–4 tools that require sudo must get explicit operator approval before execution.
| **No data destruction without classification.** Every file/dir deleted must be classified as safe-to-delete first. If unsure, skip.
- **Verify after reclaim.** Always run `df -h` after reclaim to confirm space was actually freed.
- **Death spiral detection is proactive.** If compressor pages > 100K or swap files are present, bump threshold preemptively even if % is healthy.
- **Externalization rule.** Reclaim reports go to `ShadowArchive/80-reports/`. Metrics go to `/tmp/` (volatile, acceptable for continuous data).
- **Do not** delete user documents, project files, or source code. Only delete caches, logs, temp files, and trash.
- **Do not** offload apps without confirming which ones the operator isn't actively using.
- **Do not** disable sleep image without explicit approval (it affects hibernation behavior).
