---
name: PPTX Agent
description: Use when creating, editing, or validating PowerPoint presentations. Covers python-pptx, html2pptx, template workflows, visual validation, alignment rules, table rendering, and text positioning. For branded presentations, load the domain agent (e.g., pptx-agent/astemo/).
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: pptx
  lifecycle: active
  canonical_slug: astemo-pptx-agent
  icon_style: craft-category-glyph-v1
---

# PPTX Agent

Base layer for all PowerPoint work. Generic capabilities that work with any brand or style.

## Architecture

```
PPTX Agent (this file — base capabilities)
  └── Domain Agent (e.g., astemo/SKILL.md — brand tokens, templates, voice)
        └── Session Context (project content, role language, audience)
```

Lower layers subsume upper layers. A domain agent IS a PPTX Agent with brand plugins. Load the base first, then layer domain context on top.

## When to Activate

Any task involving `.pptx` files: creation, editing, analysis, validation, template work, format conversion.

## Core Tools

| Tool | Use |
|------|-----|
| python-pptx | Programmatic PPTX creation/editing |
| html2pptx.js | HTML → PPTX via PptxGenJS (official skill) |
| markitdown | Text extraction from PPTX |
| qlmanage | macOS Quick Look export to images |
| LibreOffice | High-fidelity PDF/PNG export |
| PptxGenJS | JavaScript PPTX generation |

## Slide Dimensions

| Aspect | Width | Height |
|--------|-------|--------|
| Widescreen 16:9 | 13.33" (12192000 EMU) | 7.50" (6858000 EMU) |
| Standard 4:3 | 10.0" (9144000 EMU) | 7.50" (6858000 EMU) |

Default: widescreen. Match the template.

## Table Rendering Rules

- Explicit column widths — never auto-fit. Calculate: `col_widths = [total_width * pct for pct in col_pcts]`
- Header font: bold, same family as body
- Body font: 10-12pt (not smaller — tables must be readable at presentation distance)
- Alignment: left-align text columns, right-align or center-align numeric columns
- Alternating row shading optional but recommended for data-dense tables
- Table borders: thin (0.5-1pt), consistent color (usually gray)
- Cell padding: 0.05"-0.1" horizontal, 0.03"-0.05" vertical

## Text Positioning Rules

- Content top margin: minimum 0.75" from slide top (leave room for title/header)
- Content bottom margin: minimum 0.5" from slide bottom
- Side margins: 0.5"-0.75" left/right
- Bullet indent: 0.25" per level
- Line spacing: 1.0-1.2x for body, 1.0x for dense content
- Never overlap text boxes — check positions before placing
- Paragraph spacing: 4-8pt before/after for body text

## Visual Validation Workflow

```
create PPTX → export to images → review → fix issues → re-export → done
```

### Export Methods (priority order)

1. **Quick Look** (fastest): `qlmanage -t -s 1280 -o /tmp/pptx-review/ file.pptx`
2. **LibreOffice** (higher fidelity): `soffice --headless --convert-to png --outdir /tmp/ file.pptx`
3. **Thumbnail grid**: `python scripts/thumbnail.py file.pptx` (from official pptx skill)

### Review Checklist

- Text not overflowing boxes
- No overlapping elements
- Consistent margins across slides
- Fonts rendered correctly (no tofu/missing glyphs)
- Colors match template/spec
- CJK characters render (when applicable)

Core rule: "I can't view images" is never acceptable. Export first, then find a vision-capable path.

## Template Workflow (generic)

```
find template → copy → inspect layouts → map content → build → validate
```

1. Copy template with `shutil.copy2()` before opening
2. Inspect layouts: `for i, l in enumerate(prs.slide_layouts): print(i, l.name)`
3. Match layouts to content by shape count and placeholder types
4. Use `replace.py` (official pptx skill) or direct python-pptx for content
5. Always validate visually before delivering

## Anti-Patterns

| Problem | Fix |
|---------|-----|
| Text overflow | Check character count against box size. Shrink font or expand box. |
| Text overlap | Explicit positioning with margins. Never use auto-layout for critical content. |
| Inconsistent margins | Define a margin grid at slide start. Use it everywhere. |
| Missing font fallback | Check installed fonts. Prefer Arial, Calibri — universal. |
| Wrong colors | Use named constants or hex values from a central tokens file. Never eyeball. |
| AI cliches in content | See anti-AI voice rules in domain agents. Write like an engineer. |

## Domain Agents

For branded presentations, load the domain agent AFTER this base:

| Domain | Path |
|--------|------|
| Astemo | `pptx-agent/astemo/SKILL.md` |
| (add more) | |

## Relation to Official pptx Skill

The official `pptx` skill (`~/.agents/skills/official/skills/pptx/`) provides heavy reference: html2pptx.md, ooxml.md, scripts (inventory.py, replace.py, rearrange.py, thumbnail.py). This PPTX Agent provides the workflow layer that uses those tools. Read both for complete coverage.

## Crystallized From

- Session: 2026-04-16
- Pattern: Operator's 3-layer agent architecture (base → domain → session)
- Original scattered skills: astemo-pptx-templates, astemo-pptx-copy-reuse, pptx-visual-validation
