---
name: Skill Surgery R&D
description: Audit, repair, merge, and research agent skills as a governed R&D loop. Use when the operator asks to find skill gaps, do skill surgery, improve skill triggers, merge duplicate/deprecated skills, research new skills, or develop reusable agent capabilities.
icon: icon.svg
metadata:
  category: memory/context
  family: skills
  lifecycle: active
  canonical_slug: skill-surgery-rd
  icon_style: craft-category-glyph-v1
---

# Skill Surgery R&D

Governed loop for turning a large skill garden into a smaller, sharper capability system.

## Operating rule

Do not start by writing a new skill. First inventory what exists, identify overlap, and decide whether to **redirect**, **trim**, **split**, **merge**, or **create**.

## Workflow

1. **Inventory**
   - Scan all reachable `SKILL.md` files.
   - Capture slug, name, description, lifecycle/deprecated markers, line count, canonical source, and replacement target.
   - Save machine-readable output under `data/skill-rd/` or `ShadowArchive/80-reports/`.

2. **Classify gaps**
   - `missing-lane`: recurring task has no skill.
   - `trigger-gap`: skill exists but description will not trigger it.
   - `overlap`: two or more skills cover the same user intent.
   - `stale-lane`: deprecated/legacy skill still contains long active-looking instructions.
   - `runtime-gap`: skill describes a workflow but lacks deterministic scripts/checks.

3. **Choose surgery type**
   - **Redirect** deprecated skills to current canonical skills; keep only migration guidance.
   - **Trigger repair** by rewriting frontmatter description with concrete "Use when..." triggers.
   - **Trim** long `SKILL.md` files; move rare detail to `REFERENCE.md`.
   - **Merge** only after confirming one canonical owner and preserving useful gotchas.
   - **Create** only when no existing skill owns the lane.

4. **Research before invention**
   - For external/tool-specific skills, verify current docs or product state first.
   - Store source links and date in the skill or companion `RESEARCH.md`.
   - Never crystallize stale version numbers or pricing into a skill without a freshness note.

5. **Validate**
   - Run skill validation when available.
   - Run deterministic helper scripts for generated inventories.
   - Report bounded completion: files changed, evidence used, residual risk, next check.

## Bundled scripts

```bash
# inventory skills and write JSON reports
node /Users/sdluffy/.agents/skills/skill-surgery-rd/scripts/audit-skills.mjs --out=data/skill-rd

# dry-run or apply reversible deprecated-skill redirects from a mapping file
node /Users/sdluffy/.agents/skills/skill-surgery-rd/scripts/redirect-deprecated-skill.mjs --mapping=data/skill-rd/map.json
node /Users/sdluffy/.agents/skills/skill-surgery-rd/scripts/redirect-deprecated-skill.mjs --mapping=data/skill-rd/map.json --apply

# lexical trigger clustering for semantic duplicate triage
node /Users/sdluffy/.agents/skills/skill-surgery-rd/scripts/cluster-skill-triggers.mjs --inventory=data/skill-rd/skill-inventory.json --out=data/skill-rd/semantic-duplicate-clusters.json
```

## Description checklist

A good skill description:

- starts with the capability, not vague "helps with..." language
- includes concrete trigger phrases or task classes
- fits under 1024 characters
- says when to use it and when not to use it if overlap exists
- points deprecated users to the replacement skill

## Surgery safety

- Do not delete skills during exploratory surgery; redirect first.
- Do not edit archived history unless it is still surfacing as an active trigger problem.
- Avoid making giant omnibus skills; prefer one canonical router plus focused subskills.
- Preserve operator-specific boundary rules when consolidating Astemo/enterprise skills.

## Minimum report shape

```md
# Skill R&D Report

## Inventory
- total skills
- duplicate names/slugs
- deprecated/shim/long counts

## Top gaps
| Gap | Evidence | Proposed surgery | Priority |

## Surgeries applied
| File | Change | Verification |

## New skill candidates
| Candidate | Why new, not update | Inputs needed |
```

## Pipeline

```
Input (trigger: skill gap, skill surgery, merge skills, improve triggers, audit skills)
  ↓
Inventory (scan all reachable SKILL.md → capture slug, name, desc, lifecycle, line count)
  ↓
Classify Gaps (missing-lane, trigger-gap, overlap, stale-lane, runtime-gap)
  ↓
Choose Surgery (redirect, trigger repair, trim, merge, create)
  ↓
Research Before Invention (verify external docs, product state, freshness)
  ↓
Execute Surgery (edit skill files, redirect deprecated, update triggers)
  ↓
Validate (skill_validate, helper scripts, bounded completion report)
  ↓
Artifact Generation
  ├── data/skill-rd/skill-inventory.json          (machine-readable inventory)
  ├── data/skill-rd/semantic-duplicate-clusters.json (overlap map)
  ├── ShadowArchive/80-reports/skill-rd-YYYY-MM-DD.md  (surgery report)
  └── Updated SKILL.md files (the surgeries themselves)
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Full audit → classify → propose surgeries | `skill surgery`, `audit skills`, `find gaps` |
| `inventory` | Scan and list all skills with metadata | `inventory skills`, `list skills` |
| `gaps` | Only gap classification (no surgery) | `skill gaps`, `what's missing` |
| `overlaps` | Only overlap/duplicate detection | `duplicate skills`, `overlaps` |
| `redirect <old> <new>` | Redirect a deprecated skill to its replacement | Specific redirect request |
| `merge <a> <b>` | Merge two overlapping skills into one canonical | Specific merge request |
| `trim <skill>` | Trim a long skill, move detail to REFERENCE.md | Specific trim request |
| `research <topic>` | Research new skill candidates for a topic | `research skill for X` |

## Artifact Routing

| Artifact | Path | Purpose |
|----------|------|----------|
| Skill inventory | `data/skill-rd/skill-inventory.json` | Machine-readable catalog |
| Duplicate clusters | `data/skill-rd/semantic-duplicate-clusters.json` | Overlap analysis |
| Surgery report | `ShadowArchive/80-reports/skill-rd-YYYY-MM-DD.md` | What was done and why |
| Redirect map | `data/skill-rd/map.json` | Deprecated → canonical mapping |

## Fallback Chain

1. **Primary:** Bundled scripts (`audit-skills.mjs`, `cluster-skill-triggers.mjs`, `redirect-deprecated-skill.mjs`) → manual review → execute
2. **Bundled script fails:** Perform inventory manually via `find + grep` across skill directories; write equivalent JSON
3. **`data/skill-rd/` not writable:** Fall back to `ShadowArchive/80-reports/` for all artifacts
4. **Skill validation unavailable:** Manual schema check (frontmatter, required fields, non-empty content); note that tool validation was skipped
5. **Last resort:** Chat-based audit with inline findings; explicitly state that no machine-readable inventory was produced

## Prerequisites

- Node.js for bundled scripts (`audit-skills.mjs`, `cluster-skill-triggers.mjs`, `redirect-deprecated-skill.mjs`)
- Write access to `data/skill-rd/` or `ShadowArchive/80-reports/`
- Read access to all skill directories (`~/.agents/skills/`, `.agents/skills/`, project-level skills)
- `skill_validate` MCP tool or manual schema knowledge for validation

## Error Handling

| Failure | Recovery |
|---------|----------|
| Script execution fails (syntax error) | Run manual `find + grep` inventory; note script failure in report |
| Skill file has malformed YAML frontmatter | Flag in inventory; do not auto-fix; propose manual repair |
| Circular redirect detected (A → B → A) | Block redirect; report cycle; require manual resolution |
| Merge target has conflicting gotchas | Preserve both sets of gotchas in merged file; flag for operator review |
| Too many skills to audit (>500) | Prioritize active + non-deprecated; sample archived for stale-lane check |
| Permission denied on skill directory | Report; skip; audit accessible directories only |

## Contract

- **Do not delete skills during exploratory surgery.** Redirect first; deletion requires explicit operator approval after review.
- **Do not edit archived history** unless it is still surfacing as an active trigger problem.
- **Do not create giant omnibus skills.** Prefer one canonical router plus focused subskills.
- **Research before invention.** Never crystallize stale version numbers, pricing, or API shapes without a freshness note.
- **Preserve operator-specific boundaries** when consolidating Astemo/enterprise skills.
- **Externalization rule.** All surgery reports go to `ShadowArchive/80-reports/` or `data/skill-rd/`. Never leave the only skill audit in chat.
- **Do not** auto-merge skills without operator review of the proposed merge.
- **Do not** modify skill descriptions to be vague or over-broad just to improve trigger coverage.

## Crystallized From

- Session: 2026-05-02 skill garden audit after operator asked to "find gaps and do skill surgeries and research and development of new skills."
- Pattern: skill work needs a first-class audit → classify → surgery → research → validate loop, rather than ad hoc creation.
