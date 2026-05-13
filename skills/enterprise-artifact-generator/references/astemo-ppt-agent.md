# Astemo PPT Agent Artifact Pattern

Use for Astemo PPTX agent, Electron app/plugin, Obsidian research, staff meeting report, and executive app work.

## Canonical Research Folder

```text
Research/Codex + Obsidian/Astemo PPT Agent/
```

Current validated family:

- `Astemo PPT Agent Electron Plugin Spec.md`
- `astemo-ppt-agent-md-mmd-evolution.json`
- 10 companion `.mmd` files
- schemas for deck spec, executive brief, staff source, relay packet
- synthetic fixtures
- `scripts/validate-research.py`
- `scripts/staff-source-to-executive-brief.py`

## Core Model

- PPTX is output, not the only source of truth.
- Staff meeting report becomes an `ExecutiveBrief` data model first.
- Electron app owns operator workflow.
- Obsidian plugin owns Markdown/Mermaid evolution.
- Codex bridge performs bounded tasks only.
- iCloud print relay is PDF/markup output only.
- Dropbox relay is sanitized async A2A packet only.

## First Build Slice

Start with staff meeting report executive app:

1. synthetic staff source
2. convert to `ExecutiveBrief`
3. generate 5-slide executive deck
4. export print-safe PDF packet
5. write Obsidian summary note
6. optional sanitized Dropbox packet

## Must Preserve

- Astemo template master and layout anchors
- Arial
- red structural chrome
- MD/MMD evolution manifest
- relay scrub gates
- no arbitrary shell execution

