---
name: Algorithm State Engine
description: 'Build interactive algorithm visualizations with timeline-based state snapshots, canvas rendering, and playback controls. Single-file HTML prototypes with zero dependencies. Trigger on "visualize algorithm", "interactive visualization", "algorithm animation", "step-through", "code playback", "educational tool", "DSA visualizer".'
icon: icon.svg
metadata:
  depends_on: [shadow-patterns]
  category: tools/visualization
  family: visualization
  lifecycle: active
  canonical_slug: algorithm-state-engine
  icon_style: craft-category-glyph-v1
  openclaw_surfaces:
    - codex
    - craft-agents
---

# Algorithm State Engine

Build interactive algorithm visualizations using the Timeline Snapshot pattern (shadow-patterns #36). Produces single-file HTML prototypes with zero dependencies that demonstrate full playback, code highlighting, variable inspection, and custom inputs.

## When to Use

- Visualizing sorting, searching, graph, tree, or DP algorithms
- Building educational tools or interactive textbooks
- Prototyping algorithm animations before production implementation
- Creating interview prep visualizers
- Any task requiring step-through execution with visual rendering

## Architecture

```
Algorithm Recorder (yields snapshots)
    ↓
Timeline (Snapshot[])
    ↓
Playback Controller (manages step index)
    ↓
Renderer (pure function: Snapshot → Canvas/DOM)
    ↓
UI (code highlight + variable inspector + controls)
```

## Core Components

### 1. AlgorithmEngine (State Machine)

```javascript
class AlgorithmEngine {
  constructor() {
    this.timeline = [];
    this.step = -1;
    this.playing = false;
    this.speed = 400;     // ms per step
    this.interval = null;
    this.onStep = null;    // callback: (Snapshot) → void
    this.onPlayState = null; // callback: (playing: bool) → void
  }

  load(timeline) { /* load snapshots, go to step 0 */ }
  play() { /* auto-advance at this.speed */ }
  pause() { /* stop auto-advance */ }
  stepForward() { /* advance one step */ }
  stepBack() { /* go back one step */ }
  goToStart() / goToEnd() { /* jump */ }
  seekTo(fraction) { /* jump to percentage */ }
  setSpeed(ms) { /* change playback speed */ }
}
```

### 2. Snapshot Schema

```javascript
{
  line: number,           // highlighted source line
  desc: string,           // human-readable description
  vars: { [key]: value }, // variable bindings
  arr: number[],          // current array state
  comparing: number[],    // indices being compared (highlight purple)
  swapped: number[],      // indices being swapped (highlight red)
  sorted: number[],       // indices known sorted (highlight green)
  code: string[],         // source lines for display
  // Optional: tree, graph, auxData for complex structures
}
```

### 3. Recorder Pattern

Each algorithm is a function that takes input and returns `Snapshot[]`:

```javascript
function recordBubbleSort(input) {
  const arr = [...input];
  const timeline = [];
  const code = [/* Python source lines */];

  function snap(line, desc, vars, comparing=[], swapped=[], sorted=[]) {
    timeline.push({ line, desc, vars:{...vars}, arr:[...arr],
                    comparing:[...comparing], swapped:[...swapped],
                    sorted:[...sorted], code });
  }

  // Record initial state
  snap(0, 'Start', { n: arr.length });

  // Run algorithm, calling snap() at each interesting point
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n-i-1; j++) {
      snap(3, `Compare arr[${j}] vs arr[${j+1}]`, {...}, [j, j+1]);
      if (arr[j] > arr[j+1]) {
        [arr[j], arr[j+1]] = [arr[j+1], arr[j]];
        snap(5, `Swap!`, {...}, [], [j, j+1]);
      }
    }
  }

  snap(6, 'Done', {}, [], [], [...Array(n).keys()]);
  return timeline;
}
```

### 4. Canvas Renderer (Bar Charts)

```javascript
function renderBars(canvas, state, maxVal) {
  // DPR-aware setup
  // For each element in state.arr:
  //   - Calculate bar geometry (x, y, width, height)
  //   - Color by role: comparing=purple, swapped=red, sorted=green, default=gray
  //   - Draw bar with rounded top corners
  //   - Draw value label above bar
  //   - Draw index label below bar
}
```

### 5. UI Wiring

```javascript
window._engines = {};
window._runSort = function(type) {
  const vals = parseInput(`input-${type}`);
  const timeline = recorders[type](vals);
  const engine = new AlgorithmEngine();
  window._engines[type] = engine;
  engine.load(timeline);

  engine.onStep = (state) => {
    renderBars(document.getElementById(`canvas-${type}`), state, maxVal);
    updateCodeHighlight(`code-${type}`, state);
    updateVarInspector(`vars-${type}`, state);
    updatePlayback(...);
  };

  engine.play();
};
```

## Rendering Variants

### Bar Charts (Sorting)
- Height proportional to value
- Color by role (comparing/swapping/sorted)

### Cell Grids (Arrays, Hash Tables)
- Fixed-size cells with value centered
- Color by role (active/found/window)

### Tree Nodes (BST, Heaps)
- Circle nodes with edges
- Position calculated from tree level + index
- Color by role (visiting/comparing/found)

### Graph Nodes (BFS, DFS, Dijkstra)
- Circle nodes with weighted edges
- Force-directed or grid layout
- Color by role (unvisited/visiting/visited/frontier)

### Grid Cells (Matrices, DP)
- 2D cell array with value centered
- Color by role (current/computing/computed)

## Null Guards (Critical)

When navigating between pages, old engine callbacks fire on destroyed DOM:

```javascript
engine.onStep = (state) => {
  const canvas = document.getElementById(`canvas-${type}`);
  if (!canvas) return; // ← critical null guard
  renderBars(canvas, state, maxVal);
  // Same for all DOM updates:
  const codeEl = document.getElementById(`code-${type}`);
  if (!codeEl) return;
  // ...
};
```

## Pyodide Integration

For real Python execution in problems:

```javascript
let pyodideInstance = null;

async function initPyodide() {
  const script = document.createElement('script');
  script.src = 'https://cdn.jsdelivr.net/pyodide/v0.26.4/full/pyodide.js';
  script.onload = async () => {
    pyodideInstance = await loadPyodide();
  };
  document.head.appendChild(script);
}

async function runPython(code) {
  if (!pyodideInstance) return null; // fallback to JS
  pyodideInstance.runPython('import io, sys; sys.stdout = io.StringIO()');
  pyodideInstance.runPython(code);
  return pyodideInstance.runPython('sys.stdout.getvalue()');
}
```

Always provide a JS `new Function()` fallback for offline use.

## File Structure (Single-File)

```
index.html
├── <style> — Design tokens, layout, components (~300 lines)
├── <body> — Sidebar + content area (~20 lines)
└── <script>
    ├── AlgorithmEngine class (~60 lines)
    ├── Recorders (one per algorithm, ~30-50 lines each)
    ├── Renderers (bar, cell, tree, graph — ~40 lines each)
    ├── Page renderers (one per chapter, ~100-200 lines each)
    ├── UI update functions (~80 lines)
    ├── Problem system (~60 lines)
    ├── Pyodide integration (~30 lines)
    └── Init + event handlers (~20 lines)
```

## Size Budget

| Component | Target |
|-----------|--------|
| Total file | < 100 KB |
| Per algorithm recorder | < 2 KB |
| Per renderer | < 2 KB |
| Per chapter page | < 15 KB |
| Core engine + UI | < 10 KB |

## Gotchas

- **Snapshot explosion**: Algorithms on large inputs produce thousands of snapshots. Cap at ~200 steps or use stride-based sampling.
- **Canvas DPR**: Always scale by `devicePixelRatio` or text appears blurry on Retina.
- **Timeline scrubbing**: The clickable timeline bar needs `getBoundingClientRect()` math, not `offsetX`.
- **Cross-page null guards**: When navigating between chapters, old engine intervals still fire. Null-guard ALL DOM access in onStep callbacks.
- **roundRect polyfill**: Older browsers may not support `ctx.roundRect()`. Use `ctx.moveTo/lineTo/arc` fallback if needed.
- **Pyodide CDN**: Works offline only if previously cached. Always provide JS `new Function()` fallback for problem execution.
- **Content moat**: The engine is ~10 KB. The educational content (670 pages, 300 visualizations) is the product, not the engine. Don't confuse tech replication with product replication (shadow-patterns #37).

## Crystallized From

- Session: 2026-05-04 (session 260503-brisk-crow)
- Original task: Reverse-engineer cartesian.app DSA Interactive Handbook, then build a working prototype
- Pattern extracted: Algorithm State Engine (Timeline Snapshots) — shadow-patterns #36
- Evidence: 74KB single-file HTML with 6 algorithm visualizations, all running with 0 JS errors after null-guard fixes
- Verification: Browser-tested with play/pause/step/rewind/seek on all engines, cross-page navigation with 0 errors
