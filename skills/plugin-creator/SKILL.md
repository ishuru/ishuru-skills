---
name: Plugin Creator
description: 'Use when the user needs: Create, modify, adapt, or port OpenCode plugins and SHADOW runtime adapters. Use when adding an OpenCode plugin, changing hooks, building a TUI plugin, adapting an existing internal plugin, or turning a learned plugin pattern into a reusable local plugin workflow.'
icon: icon.svg
metadata:
  category: mesh/shadow
  family: mesh
  lifecycle: active
  canonical_slug: plugin-creator
  icon_style: craft-category-glyph-v1
---

# Plugin Creator

## Overview

Use this skill to build OpenCode plugins the way this machine actually runs them: thin adapters, durable truth in `ShadowArchive`, and target-correct module shape.

## Decide the plugin target first

Do not start coding until the target is clear.

### 1. Event/server-style OpenCode plugin
- Use when you need hooks like `tool.execute.before`, `tool.execute.after`, `session.*`, `message.*`, or `event`.
- Local/global file plugins auto-load from:
  - `~/.config/opencode/plugins/`
  - `.opencode/plugins/`
- Current host also has loadable files in `~/.config/opencode/plugin/`; treat `plugins/` as the clearer default unless compatibility requires both.
- Basic shape:

```ts
import type { Plugin } from '@opencode-ai/plugin'

export const MyPlugin: Plugin = async ({ project, client, $, directory, worktree }) => {
  return {
    event: async ({ event }) => {
      // hook logic
    },
    "tool.execute.before": async (input, output) => {
      // mutate or block tool call
    },
  }
}
```

### 2. TUI plugin
- Use when you need commands, routes, dialogs, slots, theme behavior, or sidebar/home UI.
- TUI plugin config lives in `tui.json`.
- TUI modules are **target-exclusive** and must `default export { id?, tui }`.
- A single module cannot export both `server` and `tui`.
- Local TUI plugins must be explicitly listed in `tui.json`; there is no auto-discovery.

### 3. SHADOW adapter plugin
- Use when the plugin is only the runtime bridge and the real logic should live in `ShadowArchive`.
- Keep the plugin thin and reversible.
- Put durable rules, registries, and heavy logic in SHADOW-managed files.
- Preferred shape: tiny bootstrap file + separate reusable library.

## Read in this order

1. `ShadowArchive/10-projects/shadow-plugins/README.md`
   - substrate rules, thin adapter policy, local bridge targets
2. `ShadowArchive/10-projects/opencode-ishuru/packages/web/src/content/docs/plugins.mdx`
   - global/project plugin loading, hook/event model, dependency rules
3. `ShadowArchive/10-projects/opencode-ishuru/packages/opencode/specs/tui-plugins.md`
   - TUI module contract, install rules, target exclusivity, API surface
4. Smallest local examples:
   - `~/.config/opencode/plugin/confirmo-plugin.ts`
   - `ShadowArchive/10-projects/shadow-plugins/runtime/opencode/plugins/shadow-connect.js`
   - `ShadowArchive/10-projects/shadow-plugins/runtime/opencode/plugins/shadow-connect-lib.js`
5. Behavior tests:
    - `ShadowArchive/10-projects/shadow-plugins/runtime/opencode/plugins/shadow-connect.test.js`
    - OpenCode plugin loader/lifecycle tests in `ShadowArchive/10-projects/opencode-ishuru/packages/opencode/test/cli/tui/`

## Current host reality

- `~/.config/opencode/opencode.json` currently has `"plugin": []`
- OpenCode still loads local file plugins from config-directory plugin surfaces
- Live loaded plugins on this host include:
  - `~/.config/opencode/plugin/confirmo-plugin.ts`
  - `~/.config/opencode/plugins/agent-border-glow.js`
  - `~/.config/opencode/plugins/superpowers.js`
  - `~/.config/opencode/plugins/shadow-connect.js`
- That means raw config is not the whole truth; local auto-discovery matters
- Preferred default path is `plugins/`, but this host also tolerates `plugin/`

## Workflow

### 1. Choose the narrowest useful surface

- If you only need hooks/events → build an event/server-style plugin.
- If you need commands, routes, dialogs, theme, slots → build a TUI plugin.
- If the behavior belongs in SHADOW, not OpenCode → build a thin adapter.

Default to the thinnest surface that solves the problem.

### 2. Copy the smallest working example

- For event/plugin lifecycle work, start from `confirmo-plugin.ts`.
- For SHADOW adapter work, start from `shadow-connect.js` + `shadow-connect-lib.js`.
- For TUI work, start from the module shape in `tui-plugins.md`.

Do not invent structure from scratch if an existing plugin already matches 80% of the problem.

### 3. Keep the plugin thin

- Plugin file = wiring, hooks, bootstrap, small transforms.
- Shared logic = separate `*-lib.js` or repo library.
- Durable config and truth = `ShadowArchive` registries/configs.
- Runtime-local state = adapter state only, not canonical memory.

### 4. Write the failing test first

- Prefer `node --test` for small OpenCode plugins.
- Test:
  - exports/module shape
  - env-driven behavior
  - hook behavior
  - workspace detection / context assembly if relevant
- Use the real implementation. Do not mock away the core behavior unless the platform seam forces it.

### 5. Install or wire it correctly

#### Local file plugin
- Put file in `~/.config/opencode/plugins/` for global use
- Put file in `.opencode/plugins/` for project-local use
- On this host, the durable pattern is:
  - support files live in a bundle subdirectory
  - one root-level `.js` entry is what OpenCode actually autoloads
  - use a symlinked root entry when the plugin needs helper files beside it

#### npm/plugin config entry
- Add package spec to `opencode.json` or `tui.json` depending on target
- If local plugins need external packages, add `package.json` in the config dir so OpenCode can install deps

#### SHADOW-managed adapter
- Keep source under `ShadowArchive/10-projects/shadow-plugins/runtime/opencode/plugins/`
- Use bridge/install scripts when appropriate:
  - `ShadowArchive/10-projects/shadow-plugins/scripts/sync-local-bridges.sh`
  - `ShadowArchive/10-projects/shadow-plugins/scripts/shadow-plugin-doctor.sh`

### 6. Validate behavior

- Run focused tests first
- Then run the lightest real runtime check possible
- For TUI plugins, validate id/config shape and load path rules before any UI polish
- For SHADOW adapters, confirm the plugin only injects/bridges and does not become a second source of truth

### 7. Stop discovery when the pipe is visible

For plugin architecture study, do not keep scanning once the core pipe is already known.

Stop discovery and switch to synthesis when you have all of:

1. one canonical architecture doc
2. one runtime contract or loader entrypoint
3. one live local plugin example
4. one SHADOW adapter example
5. one reliable test file
6. one install or doctor path
7. one concrete gap or mismatch

If two scan waves return convergent answers on those points, write the durable artifact immediately:

- audit report
- patch
- crystallized skill update

Do not let parallel scans postpone closure. Parallelism is for faster evidence collection, not indefinite evidence accumulation.

## Validation matrix

### Event/server-style plugin
- syntax check
- `node --test` or equivalent focused test
- confirm expected hooks fire
- confirm local plugin load at runtime

### TUI plugin
- validate `default export { id?, tui }`
- confirm no mixed `server` export
- confirm explicit `tui.json` entry
- validate route/command/id behavior before UI polish

### SHADOW adapter
- focused tests
- bridge/symlink verification
- `ShadowArchive/10-projects/shadow-plugins/scripts/shadow-plugin-doctor.sh`
- confirm plugin only injects or reads bridge state, not durable truth

## Key decisions

- **Need hooks or event interception?** Use event/server-style plugin.
- **Need commands/routes/sidebar/home UI?** Use TUI plugin.
- **Need persistent logic or registry-backed behavior?** Put it in SHADOW, not in the plugin.
- **Need a new plugin from scratch?** Reuse the internal scaffold foundation if helpful:
  - `/Users/sdluffy/.codex/skills/.system/plugin-creator/scripts/create_basic_plugin.py`

## Missing piece

The internal scaffold foundation is useful, but it is **not** an OpenCode-native generator.

Current local answer on this machine:

- `ShadowArchive/10-projects/shadow-plugins/scripts/create_opencode_plugin.py`

Use it for minimal starter generation when you need a fast OpenCode-native base for:

- `event`
- `tui`
- `shadow-adapter`

Treat it as a helper for structure or marketplace metadata, not as proof that you already have:

- an OpenCode event/server plugin scaffold
- a TUI plugin scaffold
- a SHADOW adapter scaffold
- a default smoke-test harness

## Gotchas

- Do not mix `server` and `tui` exports in one TUI v1 module.
- Do not assume TUI file plugins auto-discover; they must be listed in `tui.json`.
- Do not let runtime-local plugin state become canonical memory.
- Do not hand-build a broad framework inside the plugin when a thin adapter plus SHADOW library is enough.
- Do not skip tests; plugin load shape errors are cheap to catch early and painful to debug later.
- Do not trust `opencode.json` alone when auditing local plugin state on this host.
- Do not keep searching after the core plugin pipe is already visible; that is a convergence failure, not diligence.
- Do not assume a subdirectory plugin bundle is auto-loaded by OpenCode on this host; only the root-level `.js` entry proved live in `opencode debug config`.

## Crystallized From

- Session: 2026-04-17, SHADOW subscription cleanup + OpenCode plugin architecture study on JARVIS
- Original task: study installed OpenCode plugin architecture, learn how to modify/adapt internal plugins, and crystallize that into a reusable plugin creation workflow
- Extracted pattern: separate plugin target selection, thin adapter discipline, SHADOW-owned truth, host-aware load auditing, and test-first plugin modification into one small reusable execution path
