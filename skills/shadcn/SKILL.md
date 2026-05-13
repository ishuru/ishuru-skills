---
name: Shadcn
description: 'Use when the user needs: shadcn/ui — init, components, composition, custom registries, theming, Tailwind. Use when initializing shadcn, adding components, composing UI, building registries, or configuring themes.'
icon: icon.svg
metadata:
  category: design/ui
  family: design
  lifecycle: active
  canonical_slug: shadcn
  icon_style: craft-category-glyph-v1
---

# shadcn/ui

**Init:** `npx shadcn@latest init` — copies source into your project.
**Add:** `npx shadcn@latest add [component]` — button, card, dialog, table, form, etc.
**Build registry:** `npx shadcn@latest build` for custom `@namespace/registry`.

Key patterns: `cn()` (clsx + tailwind-merge), dark mode via `className="dark"`, `TooltipProvider` at root, CSS variable theming (oklch), components are source code.


## Pipeline

```
Intent → Parse input → Execute API/tool → Structure output → Return
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Formatted result | Normal use |
| `json` | Raw JSON | Programmatic use |

## Artifact Routing

- Output in chat — no persistent artifacts unless operator requests

## Contract

- Never expose API tokens in output
- Respect rate limits

