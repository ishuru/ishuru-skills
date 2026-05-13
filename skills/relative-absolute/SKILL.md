---
name: Relative Absolute
description: Use when reasoning about data addressing modes, deciding where to store/display information, understanding the kernel vs session distinction, or applying SP-015 principles. Trigger on "relative", "absolute", "kernel vs session", "addressing mode", "display surface", "SP-015", "promotion pipeline", "which surface", "where should this go".
icon: icon.svg
metadata:
  category: workflow/process
  family: workflow
  lifecycle: active
  canonical_slug: relative-absolute
  icon_style: craft-category-glyph-v1
---

# Relative/Absolute — The Coordinate System (SP-015)

Every piece of data has an addressing mode. This determines storage, display surface, and refresh rate.

## The Two Modes

| Dimension | Absolute (Kernel) | Relative (SLM) |
|-----------|-------------------|-----------------|
| Addressing | SHA-256 content-addressed | Session-scoped |
| Persistence | Immutable until mutation | Volatile |
| Question | "Who am I across all time?" | "What am I doing now?" |
| Storage | SHADOW volume (1.4 MB) | RAM, caches (16 GB) |
| Display | E-paper, static web | TUI, chat |

## Display Surface Binding

| Surface | Mode | Refresh |
|---------|------|---------|
| TRMNL e-paper | absolute | 15 min |
| shadowlab.cc | absolute/public | on-deploy |
| shadow-tui | relative | real-time |
| Chat sessions | relative | per-message |
| shadow-mcp | dual | on-demand |
| GLI | generative | relative→absolute |

## Decision Framework

When creating or storing anything, ask:
1. **Is this absolute or relative?**
   - Absolute: patents, identity, persona defs, mesh topology, skills
   - Relative: current context, hot files, training signals, session state
2. **Which surface should show it?**
   - Use the binding table above
3. **Should it promote?**
   - observed → latent (SP-006) → crystallized (GLI) → absolute (kernel)

## The Proof

A Node.js import error proved it: relative paths (`../../packages/gli/`) broke across contexts. Absolute paths (`/Volumes/SHADOW/...`) work everywhere. Not a bug — a proof.

## Thesis: `docs/specs/2026-03-26-relative-absolute-thesis.md` (689 lines)
## Patent: `docs/patents/SP-015/specification.md` (285 lines, 12 claims)
