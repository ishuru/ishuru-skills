---
name: PPTX Visual Validation
description: 'Use when the user needs: Visual validation of PPTX files by exporting to images and routing to vision-capable models. Never say "I can''t view images" — export, then validate. Trigger on "validate PPTX", "check slides", "visual review", "does this look right", "screenshot slides", or whenever visual inspection of a presentation is needed.'
icon: icon.svg
metadata:
  bashPattern:
  - qlmanage -t -s
  category: docs/pptx
  family: pptx
  lifecycle: active
  canonical_slug: pptx-visual-validation
  icon_style: craft-category-glyph-v1
---

# PPTX Visual Validation

Export PPTX slides to images, then use vision-capable models for visual review. Never say "I can't view images" — find a way.

## When to Use

- After creating or modifying a PPTX file
- When the operator asks "does this look right?"
- When visual layout verification is needed (text overlap, font issues, color accuracy)
- When the operator wants to see the actual rendered output
- Before delivering a presentation to a customer (Honda, MLC, etc.)

## Core Rule

**"I can't view images with this model" is NEVER an acceptable response.**

If you can't view images directly, export the PPTX to images first, then route to a vision-capable model.

## Export Methods (priority order)

### Method 1: macOS Quick Look (preferred, no install)

```bash
qlmanage -t -s 1280 -o /tmp/pptx-preview/ file.pptx
```

- Built into macOS, zero dependencies
- Generates numbered PNG thumbnails (slide_1.png, slide_2.png, etc.)
- `-s 1280` = 1280px width, good enough for layout review
- `-o` = output directory (must exist)

```bash
mkdir -p /tmp/pptx-preview
qlmanage -t -s 1280 -o /tmp/pptx-preview/ /path/to/presentation.pptx
ls /tmp/pptx-preview/  # slide_1.png.pdf.png or similar naming
```

### Method 2: LibreOffice headless (higher fidelity)

```bash
/Applications/LibreOffice.app/Contents/MacOS/soffice --headless --convert-to png --outdir /tmp/pptx-preview/ /path/to/presentation.pptx
```

- More accurate rendering than Quick Look for complex layouts
- Requires LibreOffice installed
- Better for: font rendering, table layouts, embedded charts

### Method 3: Playwright screenshot (for HTML-based review)

1. Convert PPTX to HTML using python-pptx (extract text/positions)
2. Render HTML in browser
3. Use Playwright or Chrome DevTools MCP to screenshot
4. Review the screenshot

Use when: neither Quick Look nor LibreOffice available, or when you need interactive review.

### Method 4: python-pptx thumbnail.py

```python
from pptx import Presentation
from pptx.util import Inches, Pt
import subprocess, os

def export_slides(pptx_path, output_dir, dpi=150):
    os.makedirs(output_dir, exist_ok=True)
    subprocess.run([
        "qlmanage", "-t", "-s", str(int(dpi * 10)),
        "-o", output_dir, pptx_path
    ], check=True)
```

## Visual Review Checklist

After exporting, check images for:

### Layout
- [ ] Text doesn't overflow text boxes
- [ ] No overlapping elements
- [ ] Consistent margins and spacing
- [ ] Headers/footers aligned

### Typography
- [ ] Correct fonts rendered (Arial, Calibri, etc.)
- [ ] Font sizes appropriate for content hierarchy
- [ ] No missing font fallbacks (boxes/tofu characters)

### Color
- [ ] Brand colors accurate (#B6001A for Astemo, etc.)
- [ ] Text contrast sufficient for readability
- [ ] Background colors correct
- [ ] Chart/graph colors match template

### Content
- [ ] All text visible and readable
- [ ] Tables fully rendered (no cutoff)
- [ ] Images/charts rendered correctly
- [ ] No placeholder text remaining
- [ ] Japanese/Chinese characters render correctly (CJK validation)

### Brand Compliance
- [ ] Template layout matches corporate standard
- [ ] Logo position and size correct
- [ ] Slide numbers present
- [ ] Header/footer content correct

## Vision Model Routing

After export, route images to a vision-capable model:

### Available Models

| Model | Provider | Best For |
|-------|----------|----------|
| GPT-4o | OpenAI | General visual review, detailed layout analysis |
| Gemini Pro Vision | Google | Fast review, text-in-image OCR |
| GLM-4V | Zhipu/GLM | CJK text validation, bilingual content |

### Routing

```
1. Export PPTX to images (Method 1 preferred)
2. Read the image file(s) using the Read tool (supports images)
3. If Read tool can't process images:
   a. Use Chrome DevTools MCP to open image in browser
   b. Take screenshot via DevTools
   c. Analyze the screenshot
4. If vision model available via API:
   a. Route via shadow-connect: shadow-connect-route.py --fabric shadow-compute --request-class visual-validation
   b. Or use direct API call with image attachment
```

## Quick Validation Command

For fast validation during development:

```bash
# One-liner: export and list
mkdir -p /tmp/pptx-review && qlmanage -t -s 1280 -o /tmp/pptx-review/ "$PPTX_FILE" && ls -la /tmp/pptx-review/
```

## Integration with Other Skills

- **astemo-pptx-templates** — validate Astemo-branded presentations before delivery
- **fh4s-honda-response** — visual check Honda MLC response slides
- **astemo-xlsx** — similar visual validation for Excel (qlmanage works for .xlsx too)
- **crystallize** — if visual validation reveals a pattern, crystallize it

## Gotchas

- `qlmanage` output filenames vary by macOS version (check `ls` after export)
- Quick Look may not render embedded fonts correctly — use LibreOffice if fonts look wrong
- Large PPTX files (>50 slides) may take time to export — batch validate
- Quick Look generates `.pdf.png` suffixed files on some macOS versions
- If export directory doesn't exist, `qlmanage` fails silently

## Crystallized From

- Session: 2026-04-16
- Original issue: Agent said "I can't view images with this model" three times instead of solving the problem
- Operator correction: "Never say can't, find a way"
- Pattern: Export to image, then use vision model for review


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

