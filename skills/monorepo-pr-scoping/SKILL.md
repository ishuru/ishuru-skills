---
name: Monorepo Pr Scoping
description: Use when opening a PR in a monorepo that contains multiple projects or concern types, when a PR mixes infrastructure with project code, or when scoping a branch for a multi-project repo with AI agent contributors.
icon: icon.svg
metadata:
  category: workflow/process
  family: workflow
  lifecycle: active
  canonical_slug: monorepo-pr-scoping
  icon_style: craft-category-glyph-v1
---

# Monorepo PR Scoping

## Core Rule

**One PR = one concern type.** Branch prefix declares the concern; CI enforces that only allowed paths are touched.

## Branch Prefix → Concern → Allowed Paths

| Prefix | Concern | Allowed paths | PR required? |
|---|---|---|---|
| `agent/{ID}-{slug}` | Code implementation | `ShadowArchive/10-projects/{PROJECT}/` | Yes |
| `research/{ID}-{slug}` | Research / exploration | `ShadowArchive/03-agent-roots/{LANE}/` | Yes |
| `infra/{slug}` | Config, registry, CI | `.github/`, `.gitignore`, `WORKFLOW.md`, `AGENTS.md`, `ShadowArchive/01-registry/` | Yes (human merge) |
| `reorg/{date}-{slug}` | Volume restructuring | Any — but must be declared upfront | Yes (human sign-off) |
| `report/` | Generated outputs | `ShadowArchive/80-reports/` | **No** — commit direct to main |

## Decision Flowchart

```
Is this a generated report or doc?
  YES → commit directly to main, no branch, post path to Linear
  NO ↓

Does it touch only one project in 10-projects/?
  YES → agent/{ISSUE_ID}-{slug} branch
  NO ↓

Does it touch only research/agent-roots content?
  YES → research/{ISSUE_ID}-{slug} branch
  NO ↓

Does it only touch config/CI/registry files?
  YES → infra/{slug} branch (requires human merge)
  NO ↓

Does it reorganize dirs or move files?
  YES → reorg/{date}-{slug} branch (requires explicit plan + human sign-off)
  NO ↓

Is it touching 2+ concern types?
  → SPLIT into separate branches/PRs before proceeding
```

## How to Validate Before Opening PR

```bash
# See what you've touched
git diff origin/main --name-only

# Check branch prefix
BRANCH=$(git branch --show-current)
PREFIX="${BRANCH%%/*}"
echo "Concern: $PREFIX"

# For agent/* branches — should only see project files:
git diff origin/main --name-only | grep -v "^ShadowArchive/10-projects/"
# → any output = SCOPE VIOLATION

# For research/* branches — should only see agent-roots files:
git diff origin/main --name-only | grep -v "^ShadowArchive/03-agent-roots/"
# → any output = SCOPE VIOLATION
```

## Commit Message Convention

```
{TYPE}({SCOPE}): {SUBJECT}

{BODY - what changed and why}

Closes: {LINEAR_ID}
Touched paths: {explicit list}
Co-Authored-By: {Agent name if AI-generated}
```

Types: `code`, `research`, `infra`, `report`, `docs`

Scope: project name or concern name (`chatzip`, `codex`, `registry`, `symphony`)

**Examples:**
```
code(chatzip): fix popup rendering in Safari extension

research(codex): explore feedback loop patterns for forge swarm

infra(workflow): add scope validator CI step

report: 2026-03-15 volume reconciliation
```

## The Mixing Anti-Pattern

```
# ❌ BAD: reorg/2026-03-14 touching all of:
.gitignore
ShadowArchive/10-projects/chatzip/**   ← code
ShadowArchive/10-projects/headless-skills/**  ← code
ShadowArchive/80-reports/reorg.md     ← report
WORKFLOW.md                            ← infra

# ✅ CORRECT: Split into:
# 1. infra/2026-03-14-gitignore-workflow — just .gitignore + WORKFLOW.md
# 2. agent/SHADOW-nn-chatzip-source — just chatzip source
# 3. direct commit to main — just the report
```

## For AI Agents (Codex/Symphony)

Before opening a PR, run this self-check:
1. What is my branch prefix? → Determines allowed paths
2. `git diff origin/main --name-only` → Are all paths in my allowed zone?
3. If any paths are outside allowed zone → abort, create separate branch, commit only the in-scope changes
4. Never touch `ShadowArchive/01-registry/` unless branch is `infra/`
5. Never touch root docs (`AGENTS.md`, `WORKFLOW.md`) unless branch is `infra/`

## Cross-Project Changes (Rare)

When a feature genuinely requires touching 2+ projects:
- Branch: `agent/multi-{ID}-{slug}`
- PR title prefix: `[MULTI]`
- PR body must explain why coupling is necessary
- CI will warn; requires human approval comment: `approved: multi-scope`

## Common Mistakes

| Mistake | Fix |
|---|---|
| Reorg PR includes project source code | Extract project code to separate `agent/` PR |
| Report committed via PR | Reports go directly to main; no branch, no PR |
| Infrastructure change in `agent/` branch | Separate `infra/` PR; agent reverts infra touch |
| One PR spans 3 projects | One PR per project; if coupled, use `[MULTI]` + justify |
| `.gitignore` changed in code PR | `.gitignore` = infrastructure; split to `infra/` branch |
