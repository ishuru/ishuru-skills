---
name: Web Slide Studio
description: Use when creating browser-native HTML slide decks from scratch, convert PPT/PPTX files into responsive web presentations, or improve existing HTML slide decks. Use when the user mentions slides, decks, presentations, or wants to convert a .pptx to HTML. Use for pitch decks, technical talks, tutorials, internal reviews, and presentation redesigns.
icon: icon.svg
allowed-tools: Bash(python*), Bash(python3*), Bash(pip*), Bash(open *)
argument-hint:
- topic|pptx-path|html-path
metadata:
  category: docs/pptx
  family: docs
  lifecycle: active
  canonical_slug: web-slide-studio
  icon_style: craft-category-glyph-v1
---

# Web Slide Studio

Build presentation decks as lightweight web apps instead of static slides.

## Outcomes

Produce a single self-contained `.html` file that:
- runs directly in a browser
- includes inline CSS and JavaScript
- supports keyboard navigation
- resizes cleanly across common screen sizes
- uses motion sparingly but intentionally
- never requires scrolling inside a slide

## Operating modes

Choose one mode immediately:
1. **New deck** — create a presentation from a topic, notes, or a rough outline
2. **PPT/PPTX conversion** — extract content from a PowerPoint file and rebuild it as an HTML deck
3. **HTML enhancement** — improve an existing HTML presentation without breaking fit, navigation, or style consistency

If the user passes arguments with the command, use them to infer the mode:
- `.ppt` or `.pptx` path → conversion mode
- `.html` path → enhancement mode
- anything else → new deck mode

## Non-negotiable constraints

Apply these to every generated or modified slide:
- each slide must fit inside the viewport at `100vh` / `100dvh`
- no internal scrolling inside slides
- if content is dense, split it across more slides
- use responsive sizing with `clamp()` for typography and spacing
- include `prefers-reduced-motion` handling
- keep images viewport-aware with explicit max-height rules
- prioritize contrast, hierarchy, and legibility over decoration

Read and use `viewport-base.css` before generating final output.

## Content density limits

Use these maximums unless the user explicitly wants a denser technical slide and it still fits safely:

| Slide type | Upper limit |
|---|---|
| Title | 1 heading, 1 subtitle, optional kicker |
| Standard content | 1 heading and 4-6 bullets, or 2 short paragraphs |
| Grid | 4-6 cards |
| Code | 8-12 lines visible without scrolling |
| Quote | 1 quote plus attribution |
| Image | 1 image focus area plus a short caption |
| Comparison | 2 columns, 3 points per column |

When in doubt, create more slides.

## Workflow

### Phase 1 — Intake

For a **new deck**, ask for the following in one grouped interaction:
- purpose: pitch, tutorial, conference talk, internal review, other
- audience: who will view it and what they care about
- target length: short (5-8), medium (9-15), long (16+)
- input maturity: full content, rough notes, or topic only
- brand assets: logo, screenshots, diagrams, colors, fonts, or none
- editing preference: static output only, or editable-in-browser
- preferred filename

If the user already supplied enough of this, do not ask again.

### Phase 2 — Source analysis

#### If files or images are provided
- Use the **Read** tool to inspect text files and the **Glob** tool to locate assets
- Classify images as usable, optional, or unsuitable
- Identify what each asset supports conceptually
- Let the asset mix influence the outline rather than forcing images into a finished structure later

#### If converting PPT/PPTX
1. Use **Bash** to run `python3 ~/.Codex/skills/web-slide-studio/scripts/extract_pptx.py <input> <output_dir>`
   - If `python-pptx` is not installed, run `pip3 install python-pptx` first
2. Use **Read** to load the generated `slides.json`
3. Summarize extracted titles, text blocks, notes, and image counts for the user
4. Preserve slide order unless the user asks for a redesign
5. Preserve speaker notes as HTML comments in the final deck when available

### Phase 3 — Style discovery

Use a **show-first** approach.

Use **Read** to load `~/.Codex/skills/web-slide-studio/STYLE_GUIDES.md`.

Offer either:
- **guided style discovery**: use **Write** to generate 3 one-slide preview HTML files in `.Codex-design/slide-previews/`, then open them with **Bash** (`open <file>`)
- **direct selection**: let the user pick one named preset from `STYLE_GUIDES.md`

For guided discovery:
- ask what feeling the audience should have: confident, energized, focused, reflective
- choose 3 visibly different directions
- each preview must show typography, color system, layout rhythm, and entrance motion
- if a usable logo exists, include it in each preview
- keep previews short and self-contained

### Phase 4 — Generate the full deck

Before generation, use **Read** to load these skill support files:
- `~/.Codex/skills/web-slide-studio/viewport-base.css`
- `~/.Codex/skills/web-slide-studio/html-template.md`
- `~/.Codex/skills/web-slide-studio/animation-patterns.md`

Then use **Write** to create the final presentation as a single HTML file.

Requirements:
- inline CSS and JavaScript only
- include the full contents of `viewport-base.css`
- use a complete HTML document with semantic sections
- provide keyboard navigation and visible progress affordances
- include clear section comments in code
- keep the design distinctive to the subject matter; avoid generic default aesthetics
- if editable mode is enabled, include browser editing, local persistence, and export guidance

### Phase 5 — Quality check

If the Codex Preview MCP tools are available (`preview_start`, `preview_screenshot`, `preview_inspect`), use them to verify the deck at 1280×720 and mobile viewports.

Otherwise, verify by code review before delivery:
- no slide scrollbars (all `.slide` elements use `overflow: hidden`)
- no clipped headings or orphaned bullets
- acceptable fit at 1280×720 and a narrow mobile viewport
- image sizes remain bounded
- motion does not block readability
- focus order and contrast are sensible

If any slide fails fit, split or restructure it automatically.

### Phase 6 — Delivery

After generating:
- report the file path, chosen style, and slide count
- explain navigation keys
- point to the main variables or sections to customize
- use **Bash** to run `open <file>` to launch the deck in the default browser
- clean up temporary preview files unless the user wants to keep them

## Design rules

- Avoid stale, generic presentation tropes.
- Do not default to thin gray text, washed-out gradients, or interchangeable SaaS visuals.
- Match the visual language to the topic: technical, editorial, industrial, cinematic, academic, playful, or corporate depending on context.
- Prefer one strong idea per slide.
- Use whitespace as a structural tool, not empty filler.

## Enhancement rules for existing HTML decks

When modifying an existing deck:
- use **Read** to load the existing file before making changes
- use **Edit** to make targeted modifications rather than rewriting the entire file
- preserve working navigation and layout primitives
- count visible elements before adding more
- if adding an image would crowd the slide, move it to a new slide
- if requested edits would cause overflow, split content across continuation slides
- keep style decisions consistent unless the user asked for a full redesign

## Supporting files

All supporting files are in `~/.Codex/skills/web-slide-studio/`:

- `STYLE_GUIDES.md` — curated style directions and when to use them
- `viewport-base.css` — baseline responsive viewport rules; include in every final deck
- `html-template.md` — architecture and code expectations
- `animation-patterns.md` — motion patterns mapped to tone
- `scripts/extract_pptx.py` — PPT/PPTX extraction helper (requires `python-pptx`)
