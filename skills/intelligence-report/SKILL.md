---
name: Intelligence Report
description: Use when generating a structured markdown intelligence brief with Mermaid flowcharts, data tables, ASCII charts, scenario matrices, and blockquote takeaways. Use when the user says "intelligence brief", "research report", "write report", "synthesize findings", "crystallize research", or wants a rich markdown document from research data.
icon: icon.svg
metadata:
  depends_on: [data-dashboard]
  category: memory/context
  family: memory
  lifecycle: active
  canonical_slug: intelligence-report
  icon_style: craft-category-glyph-v1
---

# Intelligence Report

Generate structured markdown intelligence briefs with rich visual formatting.

## When to Use

- User says "intelligence brief", "research report", "write report", "synthesize findings"
- Converting fetched research/data into a structured document
- Any request for a rich markdown document with diagrams and tables
- Complement to `data-dashboard` — use this for markdown, dashboard for HTML

## Workflow

1. **Fetch data** — web_fetch, API call, transcript, or parse provided data
2. **Read design system** — ALWAYS read `reference/DESIGN-SYSTEM.md` for style tokens
3. **Structure the brief** — use this standard section order:
   - Header (title + metadata blockquote)
   - Executive Summary (2-3 sentences + architecture diagram)
   - §1 Key Metrics (table + growth diagram)
   - §2 Deep Dive (data + flowchart)
   - §3 Infrastructure/Architecture (diagram + table)
   - §4 Economics/Trends (flowchart + ASCII chart)
   - §5 Research Feed (table + pipeline diagram)
   - §6 Projections (scenario matrix + timeline diagram)
   - §7 Data Quality (confidence table)
   - §8 Key Takeaways (blockquote list)
   - Appendix (methodology)
4. **Build Mermaid diagrams** — validate each with `mermaid_validate` before embedding
5. **Write to file** — save as `.md` in session data or `reference/artifacts/examples/`

## Mermaid Diagram Types

Only these are supported by the validator — do NOT use `pie`, `timeline`, `xychart-beta`:

| Type | Header | Use For |
|:-----|:-------|:--------|
| Flowchart | `graph LR` or `graph TB` | Architecture, pipelines, data flows |
| Flowchart (alt) | `flowchart LR` | Same with more features |
| State | `stateDiagram-v2` | State machines, transitions |

### Invalid → Valid Replacements

| Invalid | Replace With |
|:--------|:-------------|
| `pie title "X"` | `graph LR` subgraph with bar-text nodes (`"NVIDIA — 62%<br/>████████"`) |
| `timeline` | `graph TD` subgraphs per year with `-->` chain |
| `xychart-beta` | ASCII bar chart in code block |

## Shadow Design System Styling for Mermaid

All diagrams use these style tokens:

```
Subgraph: fill:#12161d,stroke:#4a9eff,stroke-width:1px,color:#e8e6e3
Node:     fill:#0a0d12,stroke:#4a9eff,stroke-width:1px,color:#e8e6e3
Highlight:stroke-width:2px instead of 1px
Semantic: --gn for success, --rd for error, --am for warning, --bl for info
```

## Formatting Patterns

### Executive Summary
```markdown
> **Shadow Intelligence Brief · {{DATE}}**
> Source: [url](url) · Classification: Open · Status: Live Data
```

### Scenario Matrix
```markdown
                        Low Case           Base Case          High Case
                    ┌──────────────────┬──────────────────┬──────────────────┐
       Pessimistic  │ scenario         │ scenario         │ scenario         │
                    ├──────────────────┼──────────────────┼──────────────────┤
       Base         │ scenario         │ ██ BASELINE ██   │ scenario         │
                    └──────────────────┴──────────────────┴──────────────────┘
```

### ASCII Bar Chart
```markdown
Label A  ████████████████████  value
Label B  ████████████          value
Label C  ███                   value
```

### Key Takeaways
```markdown
> **1.** Takeaway text
>
> **2.** Takeaway text
```

## Key Decisions

- **Section numbering**: Use `§1`, `§2` style with `·` separator (e.g., `## 1 · Title`)
- **Diagram density**: 1-2 diagrams per section maximum; more in appendix
- **Table alignment**: Left-align text, right-align numbers (`:---` and `---:`)
- **Confidence intervals**: Always include where available
- **Projections**: Label clearly as "(projected)" with dashed styling

## Gotchas

- **Mermaid `pie`, `timeline`, `xychart` are NOT supported** — replace with flowcharts
- **Mermaid node text with `<br/>`** must use `[]` (rectangle) not `()` or `{}`
- **Mermaid subgraph with ID** uses `subgraph id["Display Name"]` syntax
- **Style commands** must reference node/subgraph IDs, not display names
- **Validate every diagram** with `mermaid_validate` before finalizing the document
- **Code blocks** for ASCII art use triple-backtick without language tag

## Template Location

- Template: `reference/artifacts/templates/report.md`
- Example: `reference/artifacts/examples/epochai-intelligence-report.md`

## Pipeline

```
Input (trigger: intelligence brief, research report, synthesize findings, or data to document)
  ↓
Fetch Data (web_fetch, API, transcript, parse provided data)
  ↓
Read Design System (reference/DESIGN-SYSTEM.md for Shadow tokens)
  ↓
Structure Brief (§0–§8 standard section order)
  ↓
Build Mermaid Diagrams (validate each with mermaid_validate)
  ↓
Write to File (.md in session data or reference/artifacts/examples/)
  ↓
Artifact Generation
  ├── ShadowArchive/80-reports/intel-brief-<topic>-YYYY-MM-DD.md
  └── reference/artifacts/examples/ (if reusable example)
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Full structured brief: §0–§8 with diagrams, tables, and takeaways | `intelligence brief`, `research report`, `write report` |
| `brief` | Executive summary only (§0 + §8) | Quick synthesis |
| `metrics` | §1 Key Metrics only | Data-focused request |
| `diagrams` | Mermaid diagrams only (no prose) | Visual-first request |
| `scenario` | §6 Projections with scenario matrix only | Forward-looking analysis |

## Artifact Routing

| Artifact | Path | Purpose |
|----------|------|----------|
| Full intelligence brief | `ShadowArchive/80-reports/intel-brief-<topic>-YYYY-MM-DD.md` | Durable research document |
| Reusable example | `reference/artifacts/examples/<topic>-report.md` | Template reference |

## Fallback Chain

1. **Primary:** Fetch data → read design system → structure brief → build validated Mermaid → write to file
2. **Design system not found:** Use hardcoded Shadow tokens from this skill (bg: `#12161d`, accent: `#4a9eff`, text: `#e8e6e3`)
3. **Mermaid validation fails:** Replace with ASCII art equivalent; note diagram was downgraded
4. **Data source unavailable:** Report what's missing; proceed with available data; explicitly note gaps in §7 Data Quality
5. **Write path not available:** Output inline in chat; note that file write failed
6. **Last resort:** Plain text report with no diagrams; explicitly state degraded formatting

## Prerequisites

- `mermaid_validate` tool available for diagram validation
- Access to `reference/DESIGN-SYSTEM.md` for style tokens (optional, fallback tokens in skill)
- Data source accessible (URL, file, or provided inline)
- Write access to `ShadowArchive/80-reports/` for artifacts

## Error Handling

| Failure | Recovery |
|---------|----------|
| Mermaid `pie`/`timeline`/`xychart` used | Replace with `graph LR` flowchart equivalents per replacement table |
| Mermaid node text with special chars | Escape or use `[]` rectangle syntax; avoid `()` `{}` with `<br/>` |
| Mermaid validation fails | Fall back to ASCII art in code block; note degradation |
| Data fetch fails | Report source unavailable; proceed with partial data; note in §7 |
| File write fails | Output inline; note write failure; suggest retry with mounted volume |

## Contract

- **Only valid Mermaid types.** `graph`/`flowchart`/`stateDiagram-v2` only. Never use `pie`, `timeline`, or `xychart-beta`.
- **Validate every diagram.** Call `mermaid_validate` before embedding in the final document.
- **Shadow design tokens in all diagrams.** Consistent styling across all reports.
- **Section numbering with `§` and `·` separators.** E.g., `## 1 · Key Metrics`.
- **No credential exposure.** Reports must not contain API keys, auth tokens, or internal URLs.
- **Externalization rule.** All intelligence briefs go to `ShadowArchive/80-reports/`. Never leave the only brief in chat.
- **Do not** fabricate data for missing sections. Report gaps honestly in §7 Data Quality.
- **Do not** use invalid Mermaid types even if they render in some viewers. Stick to the validated set.

## Crystallized From

- Session: 2026-04-26
- Original task: Generating a rich intelligence brief from Epoch AI web data with 8 Mermaid diagrams, tables, ASCII charts, and scenario matrices
- Pattern extracted: Structured section order, valid Mermaid replacements, Shadow design tokens in diagrams
- Key lesson: Mermaid validator only supports `graph`/`flowchart`/`stateDiagram-v2` — replace pie/timeline/xychart with flowchart equivalents
