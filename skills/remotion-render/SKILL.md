---
name: Remotion Render
description: Use when generating high-fidelity rendered frames, diagrams, dashboards, and visual compositions from React/HTML for embedding in Typora documents. Uses Remotion's <Composition> + renderStillOnWeb() to produce pixel-perfect PNG/PDF exports that replace static Mermaid or ASCII art in Markdown. Trigger on "remotion render", "render frame", "typora diagram", "high-fidelity diagram", "better rendering", "remotion still", "pixel-perfect export", or when Mermaid/ASCII isn't expressive enough for the document.
icon: icon.svg
metadata:
  depends_on: [shadow-typora-engine, cc-design-system, astemo-md-mmd-design-system]
  category: render/remotion
  family: shadow
  lifecycle: active
  canonical_slug: remotion-render
  icon_style: craft-category-glyph-v1
  tier: 1
---

# Remotion Render

Programmatic high-fidelity rendering for Typora documents. Replace static Mermaid/ASCII diagrams with pixel-perfect React compositions exported as PNG.

## When to use

| Scenario | Use this skill? |
|----------|----------------|
| Mermaid diagram looks fine | No — stick with Mermaid |
| Need precise layout, typography, gradients, shadows | Yes |
| Architecture diagram with icons and layered boxes | Yes |
| Dashboard-style stat cards in a document | Yes |
| Bilingual JP/EN table with brand tokens | Yes |
| Flowchart that Mermaid can handle | No — overkill |
| Animated or interactive preview (HTML export) | Yes — use Remotion Player |

**Rule of thumb**: If you're fighting Mermaid syntax to get layout precision, switch to Remotion.

## Core model

```text
React Component → Remotion <Composition> → renderStill() → PNG/PDF → ![alt](path) in Markdown
```

### HTML-in-canvas (new, April 2026)

The `allowHtmlInCanvas: true` flag in `renderStillOnWeb()` uses the experimental Chromium `drawElementImage` API to capture the full DOM as pixels without screenshots. This means:

- **No headless browser needed** for stills
- **Pixel-perfect** DOM capture including CSS Grid, Flexbox, backdrop-filter
- **Fonts render correctly** (no screenshot font substitution)
- Requires Chrome Canary (as of May 2026) for full `requestPaint()` support

## Project scaffold

```bash
# One-time setup per rendering project
npx create-video@latest --template blank my-render-project
cd my-render-project
npm install
```

## Rendering a still (Typora-bound)

```tsx
// src/Root.tsx
import { Composition } from "remotion";
import { MyDiagram } from "./MyDiagram";

export const RemotionRoot = () => {
  return (
    <Composition
      id="MyDiagram"
      component={MyDiagram}
      durationInFrames={1}
      fps={30}
      width={1920}
      height={1080}
    />
  );
};
```

```tsx
// src/MyDiagram.tsx
import React from "react";
import { AbsoluteFill } from "remotion";

export const MyDiagram: React.FC = () => {
  return (
    <AbsoluteFill style={{ background: "#0a0a0a", fontFamily: "system-ui" }}>
      {/* Build your composition here */}
      <div style={{ padding: 60 }}>
        <h1 style={{ color: "#e0e0e0", fontSize: 48 }}>Architecture</h1>
        {/* Boxes, arrows, gradients, shadows... */}
      </div>
    </AbsoluteFill>
  );
};
```

### CLI render to PNG

```bash
cd ~/remotion-render
npx remotion still src/index.ts MyDiagram --output=output/my-diagram.png --frame=0
```

### Server-side still render (Node)

```ts
import { renderStill } from "@remotion/renderer";

await renderStill({
  composition: { id: "MyDiagram", width: 1920, height: 1080, fps: 30, durationInFrames: 1 },
  serveUrl: "./dist",
  output: "my-diagram.png",
  frame: 0,
});
```

### Client-side still render (HTML-in-canvas)

```ts
import { renderStillOnWeb } from "@remotion/web-renderer";

const blob = await renderStillOnWeb({
  composition: { id: "MyDiagram", width: 1920, height: 1080, fps: 30, durationInFrames: 1 },
  frame: 0,
  inputProps: {},
  allowHtmlInCanvas: true,
});
```

## Embedding in Typora

In your Markdown document:

```markdown
![Architecture Diagram](./assets/my-diagram.png)
```

Standard image embed. Typora renders it inline in live preview and exports it to PDF/HTML.

### Recommended image sizes

| Use case | Width | Height |
|----------|-------|--------|
| Full-width diagram | 1920 | 1080 |
| Half-width sidebar | 960 | 540 |
| Inline icon/badge | 240 | 240 |
| Print-quality (300 DPI, 6.5" wide) | 1950 | ~1100 |

### File placement convention

```
doc-folder/
├── doc.md
└── assets/
    ├── arch-diagram.png      ← Remotion render
    ├── flow-state.png        ← Remotion render
    └── remotion-project/     ← Source project (optional, .gitignore in docs)
        ├── src/
        ├── package.json
        └── ...
```

## Props-driven compositions

All 4 compositions accept TypeScript props with sensible defaults. Pass data via `inputProps` in code, or extend the CLI.

```typescript
// Types are in src/lib/types.ts
interface MeshArchitectureProps {
  title?: string;
  subtitle?: string;
  nodes: MeshNode[];    // { id, label, sub, icon, tier }
  links: MeshLink[];    // { from, to }
}

interface ProgramDashboardProps {
  title?: string;
  stats: StatCard[];    // { title, value, delta?, color? }
  rows: TableRow[];     // { no, part, desc, status, rev }
}

interface BilingualNotesProps {
  notes: BilingualNote[]; // { no, en, jp, category, critical }
}

interface ContextDashboardProps {
  title?: string;
  mode?: string;
  metrics: Array<{ label, value, color? }>;
  activity: ActivityEntry[];  // { time, event, detail, color }
  progress: ProgressItem[];   // { label, pct, color }
}
```

## Design system integration

### ShadowTech tokens

```tsx
const SHADOW = {
  bg: "#050507",
  surface: "#0d0d12",
  surfaceHover: "#13131a",
  accent: "#8b5cf6",
  accentGlow: "rgba(139, 92, 246, 0.3)",
  text: "#e4e4e7",
  textMuted: "#71717a",
  border: "rgba(139, 92, 246, 0.15)",
  font: "system-ui, -apple-system, sans-serif",
};
```

### Codex tokens

```tsx
const CODEX = {
  bg: "#1a1a2e",
  surface: "#16213e",
  accent: "#0f3460",
  text: "#e4e4e7",
  font: "monospace",
};
```

### Astemo tokens

```tsx
const ASTEMO = {
  red: "#B6001A",
  dark: "#1a1a1a",
  light: "#F2F2F2",
  text: "#333333",
  font: "Arial, sans-serif",
};
```

## Common patterns

### Architecture diagram

```tsx
const Box: React.FC<{ x: number; y: number; w: number; h: number; label: string; color: string }> = 
  ({ x, y, w, h, label, color }) => (
  <div style={{
    position: "absolute", left: x, top: y, width: w, height: h,
    background: color, borderRadius: 8, display: "flex",
    alignItems: "center", justifyContent: "center",
    color: "#fff", fontSize: 16, fontWeight: 600,
    boxShadow: `0 4px 24px ${color}33`,
  }}>
    {label}
  </div>
);
```

### Arrow / connector

```tsx
// SVG arrow between boxes
const Arrow: React.FC<{ x1: number; y1: number; x2: number; y2: number }> = 
  ({ x1, y1, x2, y2 }) => (
  <svg style={{ position: "absolute", left: 0, top: 0, width: "100%", height: "100%", pointerEvents: "none" }}>
    <defs>
      <marker id="arrowhead" markerWidth="10" markerHeight="7" refX="10" refY="3.5" orient="auto">
        <polygon points="0 0, 10 3.5, 0 7" fill="#8b5cf6" />
      </marker>
    </defs>
    <line x1={x1} y1={y1} x2={x2} y2={y2} stroke="#8b5cf6" strokeWidth={2} markerEnd="url(#arrowhead)" />
  </svg>
);
```

### Stat card grid

```tsx
const StatCard: React.FC<{ title: string; value: string; delta?: string }> = ({ title, value, delta }) => (
  <div style={{
    background: "rgba(139, 92, 246, 0.08)",
    border: "1px solid rgba(139, 92, 246, 0.15)",
    borderRadius: 12, padding: "24px 32px",
  }}>
    <div style={{ color: "#71717a", fontSize: 14, marginBottom: 8 }}>{title}</div>
    <div style={{ color: "#e4e4e7", fontSize: 36, fontWeight: 700 }}>{value}</div>
    {delta && <div style={{ color: "#22c55e", fontSize: 13, marginTop: 4 }}>{delta}</div>}
  </div>
);
```

## Pipeline

```
1. Identify rendering need (diagram, dashboard, architecture, table)
2. Check: can Mermaid handle it? → Yes → use Mermaid
3. No → scaffold Remotion composition
4. Apply design system tokens (ShadowTech / Codex / Astemo)
5. Render still → PNG
6. Place in assets/ folder
7. Embed in Markdown with ![alt](./assets/name.png)
8. Verify in Typora live preview
```

## Quick render

**Global CLI** (callable from any directory):
```bash
remotion-render ShadowMeshArchitecture          # render mesh diagram
remotion-render AstemoProgramDashboard fh4s      # custom output name
remotion-render --list                           # list compositions
remotion-render --list --json                    # JSON output
remotion-render ShadowMeshArchitecture mesh --copy ~/Desktop  # copy to Desktop
```

**Project-level** (from `~/remotion-render/`):
```bash
npm run render:all          # all compositions
npm run render:mesh         # Shadow mesh architecture
npm run render:astemo       # Astemo program dashboard
npm run render:bilingual    # Bilingual drawing notes
npm run studio              # live preview in browser
```

**Embed in Typora**: `![alt](file:///Users/sdluffy/remotion-render/output/name.png)`

Output: `~/remotion-render/output/*.png`

## Limitations

- Remotion stills require Node.js + Chromium for server-side rendering
- HTML-in-canvas client-side rendering requires Chrome Canary (as of May 2026)
- Not a replacement for simple Mermaid diagrams — only use when precision matters
- Rendered PNGs are static; for interactive, use HTML export + Typora HTML blocks
- First render is slow (bundle + Chrome launch ~10s); subsequent renders ~2-3s

## Artifact routing

| Artifact | Path |
|----------|------|
| Remotion project | `~/remotion-render/` |
| Rendered PNGs | `~/remotion-render/output/*.png` |
| Compositions | `~/remotion-render/src/compositions/` |
| Design tokens | `~/remotion-render/src/lib/tokens.ts` |
| Shared primitives | `~/remotion-render/src/lib/primitives.tsx` |
| Typora embeds | `<doc-folder>/assets/*.png` (copy from output/) |

## See also

- [Remotion docs](https://www.remotion.dev/docs)
- [HTML-in-canvas](https://www.remotion.dev/docs/client-side-rendering/html-in-canvas)
- [renderStill()](https://www.remotion.dev/docs/renderer/render-still)
- shadow-typora-engine — Typora theme/mode control
- astemo-md-mmd-design-system — Markdown/Mermaid design system for when Remotion is overkill
