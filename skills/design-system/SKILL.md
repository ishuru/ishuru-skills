---
name: Design System
description: 'Use when the user needs: Unified design router — HTML pages, dashboards, canvas viz, components, animations in any brand (ShadowTech, Codex, Codex, Astemo, Omarchy). Use on "dashboard", "HTML page", "visualization", "canvas", "component", "animation", "design system", or any UI generation.'
icon: icon.svg
metadata:
  depends_on: [astemo,astemo-md-mmd-design-system,astemo-xlsx,cc-animation,cc-canvas-viz,cc-component,cc-dashboard,cc-design-system]
  category: enterprise/astemo
  family: design-system
  lifecycle: active
  canonical_slug: design-system
  icon_style: craft-category-glyph-v1
---

# Design System Router

| Brand | Bg | Accent | Font | Use For |
|-------|----|--------|------|---------|
| **ShadowTech** | `#0a0a0a` | Violet `#7c3aed` | Geist | Fleet, mesh, agents |
| **Codex** | `#fff` | Orange `#f97316` | Geist Mono | Dev tools |
| **GPT Codex** | Terminal adaptive | Semantic green/blue | Mono | OpenAI-style |
| **Astemo** | `#fff` | Red `#B6001A` | Arial | Corporate engineering |
| **Astemo Dark** | `#1a1a1a` | Green/blue/amber | Inter | Internal dashboards |
| **Omarchy** | Catppuccin Mocha | Aether | System | Desktop-native |

Output types: HTML Page, Dashboard, Canvas Viz, Component, Animation.

## Sub-resources (family: design-system)

These skills are brand-specific resources dispatched by this router. They share `family: design-system` for grouping.

| Slug | Brand | Type |
|------|-------|------|
| `gc-design-system` | GPT Codex | Tokens + patterns |
| `gc-html-page` | GPT Codex | HTML page |
| `gc-dashboard` | GPT Codex | Dashboard |
| `gc-canvas-viz` | GPT Codex | Canvas visualization |
| `gc-component` | GPT Codex | UI component |
| `gc-animation` | GPT Codex | Animation |
| `cc-design-system` | Codex | Tokens + patterns |
| `cc-html-page` | Codex | HTML page |
| `cc-dashboard` | Codex | Dashboard |
| `cc-canvas-viz` | Codex | Canvas visualization |
| `cc-component` | Codex | UI component |
| `cc-animation` | Codex | Animation |
| `shadowtech-design-system` | ShadowTech | Tokens + patterns |
| `shadowtech-html-page` | ShadowTech | HTML page |
| `shadowtech-canvas-viz` | ShadowTech | Canvas visualization |
| `astemo-xlsx` | Astemo | Excel styling |
| `redesign-taste-skill` | Any | Audit + upgrade process |

Astemo rules (verified from corporate PPT):
- Font: **Arial only** (never Calibri). Sizes: 36pt title, 18pt heading, 14pt body
- Accent: `#B6001A` (never `#C00000`). Text: `#232323` primary, `#4D4D4D` secondary, `#737373` muted
- Rows: alternating `#F2F2F2`. Status fills: green `#C6EFCE`, yellow `#FFEB9C`, red `#FFC7CE`
- iTerm: white bg, `#232323` fg, red cursor, red tab. Layouts: Title/Contents/Section/Standard/Last/Blank

## Astemo Markdown/MMD + Typora compatibility

Canonical cross-engine docs:

- `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/astemo-md-mmd-design-system.md`
- `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/astemo-md-mmd-tokens.json`
- Typora source theme: `/Volumes/☯Duality/spaces/professional/sduvvuru-qx/themes/typora/astemo.css`
- Typora installed theme: `~/Library/Application Support/abnerworks.Typora/themes/astemo.css`

Rules for Markdown/MMD surfaces:

1. **Astemo is visible brand; Shadow/Craft/agents are invisible infrastructure.** Do not show `Shadow Work`, agent names, or debug badges in Astemo-facing rendered docs.
2. **Portable Markdown first.** Meaning must survive without CSS: use explicit labels like `Status`, `Evidence`, `Risk`, `Next action`, Markdown tables, and Mermaid fences.
3. **CSS is an adapter, not the source of meaning.** Typora, HTML, Pandoc, Obsidian, and GitHub should all show readable content even if they ignore Astemo styling.
4. **Typora work mode uses `shadow-work.css` only as a transparent alias over `astemo.css`.** It must not inject Shadow branding.
5. **Template-preserving adapters for Office.** PPTX/XLSX/DOCX should fill approved slots/ranges/styles while preserving Astemo masters/logos/layouts/formulas.
6. **Mermaid/MMD diagrams:** prefer portable `flowchart`, `sequenceDiagram`, and `stateDiagram-v2`; validate before final; avoid HTML labels for cross-engine portability.

## Pipeline

```
Input (trigger: dashboard, HTML page, visualization, component, animation, design system)
  ↓
Brand Detection (from context, program, or explicit request)
  ↓
Output Type Selection (HTML Page, Dashboard, Canvas Viz, Component, Animation)
  ↓
Route to Sub-Skill:
  ├── ShadowTech → shadowtech-design-system, shadowtech-html-page, shadowtech-canvas-viz
  ├── Codex → cc-design-system, cc-html-page, cc-dashboard, cc-canvas-viz, cc-component, cc-animation
  ├── GPT Codex → gc-design-system, gc-html-page, gc-dashboard, gc-canvas-viz, gc-component, gc-animation
  ├── Astemo → astemo-xlsx, astemo-md-mmd-design-system
  └── Omarchy → Catppuccin Mocha tokens
  ↓
Build + Render
  ↓
Artifact Generation
  ├── HTML files (inline preview + saved)
  ├── ShadowArchive/80-reports/ (for dashboard exports)
  └── Program-specific paths (for Astemo artifacts)
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Auto-detect brand from context and route to sub-skill | Any UI generation trigger |
| `shadowtech` | Force ShadowTech brand (dark, violet) | `shadowtech dashboard`, `fleet UI` |
| `codex` | Force Codex brand (light, orange) | `codex page`, `dev tool UI` |
| `gpt-codex` | Force GPT Codex brand (terminal, semantic) | `gpt codex page`, `openai style` |
| `astemo` | Force Astemo brand (white, red `#B6001A`, Arial) | `astemo dashboard`, `engineering UI` |
| `catalog` | List all available sub-skills with brand/type matrix | `design catalog`, `what brands` |

## Artifact Routing

| Artifact | Path | Purpose |
|----------|------|----------|
| HTML pages | Inline preview + session data/ | Rendered UI output |
| Dashboard exports | `ShadowArchive/80-reports/dashboard-*.html` | Persistent dashboard snapshots |
| Astemo artifacts | Program Dropbox root or `ShadowArchive/80-reports/` | Customer-facing or internal |
| Markdown/MMD docs | Program docs/ or `spaces/professional/sduvvuru-qx/` | Astemo documentation |

## Fallback Chain

1. **Primary:** Detect brand → route to specific sub-skill → build with tokens → render
2. **Sub-skill not available:** Use brand tokens from the router table (this skill) and build directly
3. **Brand not specified:** Infer from context: Astemo program → Astemo brand; mesh/fleet → ShadowTech; dev tools → Codex; default → Codex
4. **Design tokens not found:** Use hardcoded values from this skill's brand table as minimum viable styling
5. **Last resort:** Plain HTML with no design system; explicitly note that no brand tokens were applied

## Prerequisites

- Brand-specific sub-skills installed (see Sub-resources table)
- For Astemo: `astemo-xlsx` skill for Excel styling tokens, `astemo-md-mmd-design-system` for Markdown/Mermaid
- For ShadowTech: `shadowtech-design-system` skill for dark theme tokens
- For Mermaid: `mermaid_validate` tool for diagram validation
- HTML rendering capability (browser or inline preview)

## Error Handling

| Failure | Recovery |
|---------|----------|
| Sub-skill not installed | Use tokens from this router table; note degraded styling |
| Brand ambiguity (Astemo vs ShadowTech) | Ask operator; default to Codex for generic UI, Astemo for enterprise context |
| Astemo red wrong shade (`#C00000`) | Always correct to `#B6001A`; this is non-negotiable |
| Mermaid validation fails | Replace invalid types with flowchart equivalents; see `intelligence-report` skill |
| HTML rendering fails | Save raw HTML; report; suggest opening in browser manually |

## Contract

- **Astemo red is `#B6001A`, never `#C00000`.** This is verified from corporate PPT and non-negotiable.
- **Astemo font is Arial only, never Calibri.** Verified from corporate standards.
- **Astemo is visible brand; Shadow/Craft/agents are invisible infrastructure.** Do not show Shadow branding in Astemo-facing output.
- **Portable Markdown first.** Content must be readable without CSS. CSS is adapter, not source of meaning.
- **Externalization rule.** Dashboard exports go to `ShadowArchive/80-reports/`. Astemo artifacts go to program Dropbox root.
- **Do not** mix brand tokens across outputs. One brand per artifact.
- **Do not** use `pie`, `timeline`, or `xychart-beta` Mermaid types. Use flowchart replacements.
- **Do not** inject Shadow agent names, debug badges, or internal tooling references into customer-facing output.
