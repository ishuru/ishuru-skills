---
name: Experience Capture
description: 'Use when the user needs: Capture operational experience into persistent memory. Write discoveries, error patterns, user preferences, and procedural knowledge to memory files so future sessions can operate with accumulated wisdom instead of starting fresh. Use at the end of sessions or when significant new knowledge is gained.'
icon: icon.svg
command: remember
user_invocable: true
metadata:
  category: memory/context
  family: memory
  lifecycle: active
  canonical_slug: experience-capture
  icon_style: craft-category-glyph-v1
---

# Experience Capture — Persistent Learning System

## Memory Architecture
```
~/.Codex/projects/-Users-leo-mcp-gateway/memory/
├── MEMORY.md              ← Auto-loaded every session (keep <200 lines)
├── experience-framework.md ← How experience works (meta-knowledge)
├── error-patterns.md       ← What goes wrong and how to fix it
├── rendering-capabilities.md ← Output format knowledge
└── [topic].md              ← Deep-dive files linked from MEMORY.md
```

## What To Capture

### After discovering something new about the environment:
→ Update MEMORY.md with the location/path/config
→ Example: "JARVIS has shadowtech-site at ~/shadowtech-site"

### After an error is resolved:
→ Add to error-patterns.md with: Error | Cause | Fix
→ Example: "llama3.3 hangs | 70B on 8GB RAM | Use llama3.2:3b instead"

### After learning a user preference:
→ Add to MEMORY.md under "User Preferences"
→ Example: "Prefers Chrome Dev with Sabbu profile"

### After developing a new procedure:
→ Consider creating a new skill in ~/.Codex/skills/[name]/SKILL.md
→ Or add to MEMORY.md if it's a one-liner

### After completing a complex task:
→ Distill the key learnings, don't dump the whole session
→ Focus on: what would I want to know if I had to do this again?

## Capture Rules
1. Check existing memory first — update don't duplicate
2. Keep MEMORY.md under 200 lines (it's auto-loaded)
3. Use separate files for deep topics, link from MEMORY.md
4. Write in present tense ("X is at Y", not "X was found at Y")
5. Include concrete values (paths, ports, commands) not just concepts
6. Remove outdated info — stale memory is worse than no memory

## Experience Layers (What to look for)
1. **Environmental** — new paths, services, configs, ports
2. **Procedural** — new commands, workflows, integrations
3. **Error** — new failure modes and their fixes
4. **Preference** — new user preferences or corrections
5. **Relational** — how components connect to each other

## Sync Experience Across Mesh
After updating local memory, consider if JARVIS or other peers need the same knowledge:
```bash
scp -r ~/.Codex/skills/[new-skill] jarvis-local:~/.Codex/skills/
```
