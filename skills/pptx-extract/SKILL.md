---
name: PPTX Extract
description: Use when extracting structured content from PowerPoint files — text, tables, slide structure, notes. Use when analyzing PPTX files, reverse-engineering slide formats, or ingesting presentation content. Trigger on "extract pptx", "parse powerpoint", "read pptx", "analyze presentation".
icon: icon.svg
metadata:
  category: docs/pptx
  family: pptx
  lifecycle: active
  canonical_slug: pptx-extract
  icon_style: craft-category-glyph-v1
---

# pptx-extract — PowerPoint Content Extraction

Extract and analyze content from PPTX files using python-pptx.

## When to Use

- Reverse-engineering existing report formats (Kevin's weekly, Miyashita's staff report)
- Extracting text/tables from presentations for ingestion
- Analyzing slide structure to build PPTX templates

## Procedure

### 1. Load and enumerate slides
```python
from pptx import Presentation
prs = Presentation('file.pptx')
print(f'Slides: {len(prs.slides)}, Size: {prs.slide_width}x{prs.slide_height}')
```

### 2. Extract all content
```python
for i, slide in enumerate(prs.slides, 1):
    print(f'═══ Slide {i}/{len(prs.slides)} ═══')
    for shape in slide.shapes:
        if shape.has_text_frame:
            for para in shape.text_frame.paragraphs:
                text = para.text.strip()
                if text:
                    print(f'  {text}')
        if shape.has_table:
            table = shape.table
            for row in table.rows:
                cells = [cell.text.strip() for cell in row.cells]
                print(f'  | {" | ".join(cells)} |')
```

### 3. Detect slide types
- **Title slide**: < 5 text elements, contains date/company name
- **Section divider**: single program name (XP7G, FH4S, etc.)
- **Content slide**: multiple paragraphs, may have tables
- **Schedule slide**: contains Gantt-like table with dates

### 4. Optional: Ingest to CASER
```python
from caser.collector import ExperienceBuffer, ExperienceUnit
# Create training pairs from slide content
```

## Key Astemo PPTX patterns
- Kevin's format: 23 slides, RASIC table on slide 2, programs separated
- Miyashita's format: 12 slides, color coding (blue/yellow/red)
- Schedule PPTXs: embedded Gantt images, date tables
- VEC slides: DV test plan tables with phase/duration


## Pipeline

```
Intent → Resolve target → Execute operation → Verify result → Report
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Standard output | Normal use |
| `verbose` | Detailed diagnostics | Debugging |
| `json` | Machine-readable output | Programmatic use |

## Artifact Routing

- Reports: `ShadowArchive/80-reports/`
- Config changes: `system/controls/` or `config/system/`

## Prerequisites

- SHADOW infrastructure accessible
- Appropriate auth/SSH for mesh targets

## Contract

- Never modify production config without operator confirmation
- Report all changes with before/after diff
- Preserve existing configurations with backup

