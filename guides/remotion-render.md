# remotion-render — Guide

## Quick Start

```
remotion render a dashboard diagram for the FH4S pipeline
render frame — architecture overview
```

## When to Use

- Mermaid/ASCII isn't expressive enough
- Need pixel-perfect output for Typora documents
- Complex dashboards, architecture diagrams, data visualizations
- Visual compositions that need exact positioning and branding

## Output

- **PNG stills** — for embedding in Markdown/Typora
- **PDF exports** — for print-quality documents

## Architecture

```
React <Composition> → renderStillOnWeb() → PNG/PDF
```

## Dependencies

- Remotion (render engine)
- React (composition layer)
- Design system tokens (branding)
