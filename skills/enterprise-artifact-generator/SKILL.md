---
name: Enterprise Artifact Generator
description: 'Use when generating governed enterprise artifacts from raw work: Markdown specs, Mermaid diagrams, PPTX/deck packets, operator packets, print/PDF relay packets, and sanitized Dropbox async A2A packets. Use when the user asks to create, elevate, externalize, package, share, render, crystallize, or evolve Astemo/enterprise architecture, SOP, review, staff-meeting, IPM, or project artifacts with MD/MMD evolution and relay-safe packaging.'
icon: icon.svg
metadata:
  category: enterprise/astemo
  family: enterprise
  lifecycle: active
  canonical_slug: enterprise-artifact-generator
  icon_style: craft-category-glyph-v1
---

# Enterprise Artifact Generator

Use this skill when work must become a durable enterprise artifact rather than a chat answer or raw file dump.

## Core Rule

Build artifacts as a governed family:

1. Markdown contract
2. Mermaid projections
3. machine-readable evolution manifest
4. optional PPTX/deck output
5. sanitized relay/print packet
6. validation evidence

Do not share raw local internals into Dropbox/iCloud. Scrub first.

## Workflow

1. **Classify audience**
   - operator
   - engineering
   - executive
   - customer-safe
   - async A2A relay

2. **Choose canonical owner**
   - local/project lane owns source artifacts
   - Obsidian owns Markdown/Mermaid research
   - iCloud print relay owns PDF/markup copies only
   - Dropbox owns sanitized async A2A packets only

3. **Create the Markdown contract**
   - purpose
   - scope
   - inputs/outputs
   - ownership boundaries
   - workflow
   - acceptance criteria
   - open questions

4. **Create Mermaid projections**
   - architecture map
   - lifecycle/state diagram
   - sequence flow or safety-gate map
   - add metadata headers:

```text
%% artifact_id:
%% diagram_version:
%% governing_markdown:
%% diagram_role:
%% must_stay_consistent_with:
```

5. **Create the evolution manifest**
   - source Markdown
   - companion MMD files
   - schemas/fixtures if present
   - validation commands
   - change rules
   - patch/minor/major impact where useful

6. **Package outputs**
   - operator packet: guide, deck, selected evidence, diagrams, checklist
   - print packet: PDF, readme, source-ref, return instructions
   - Dropbox packet: executive summary, start-here, packet-map, return-contract, lifecycle, ack

7. **Validate**
   - JSON manifests parse
   - MMD headers exist
   - forbidden Astemo visual patterns absent
   - relay packet leak scan passes
   - no raw video/metadata/OCR/debug JSON unless explicitly needed and local-only

## Astemo Artifact Mode

Use for Astemo-facing architecture, SOP, staff-meeting, IPM, and review artifacts.

Rules:

- white background
- light-gray panels only where needed
- Arial only
- Astemo red `#B6001A` for structural chrome only
- compact tables
- clear borders
- no gradients
- no glass/blur
- no dark-dashboard treatment
- no purple/violet dominant styling

### Meeting Screenshot To Enterprise Artifact

Use when a recent meeting screenshot needs to become a durable Astemo/Honda work artifact.

1. Identify the actual latest screenshot by timestamp and inspect it visually.
2. Extract only visible facts first; keep ambiguous labels broad instead of inventing precision.
3. Choose the artifact shape from the content:
   - timing/SOP/support cadence -> Mermaid `gantt`
   - handoff/approval protocol -> `sequenceDiagram`
   - ownership or support boundary -> `flowchart`
4. For Honda-facing work, add a cross-region lane when needed: AM risk framing, JP support owner, bilingual evidence pack, Honda alignment gate, and customer-safe response.
5. Apply Astemo design through a render config when Mermaid source validation would reject inline theme blocks. Keep source `.mmd`, prose `.md`, render config `.json`, and rendered `.svg` together.
6. State what is screenshot-derived versus inferred, and mark durations as approximate unless the screenshot gives exact dates.

### Japanese-Company Cross-Regional Artifact Voice

Use this voice for Astemo internal artifacts that will be read by US DE, JP DE, buyers, sourcing, or quality.

Do:

- Use section labels such as `事実 / Facts`, `懸念 / Concern`, `確認事項 / Confirmation`, `依頼事項 / Request`.
- Put the schedule control map in date order before the interpretation.
- Show source date and confirmation status when a source may be stale.
- Keep conclusions modest: `current available schedule shows...`, not `the schedule proves...`.
- Use compact tables and short bullets instead of essay-like paragraphs.
- Keep bilingual labels functional, not decorative.

Avoid:

- Long English narrative where a table would be clearer.
- synthetic narrative phrasing such as `the key insight is`, `this demonstrates`, or `it is important to note`.
- Persuasive language that sounds like arguing with another department.
- Over-translating every term; only translate terms that help cross-region handoff.
- Tool/workflow tells such as `source freshness`, `extraction`, `operator correction`, `agent-ready`, `machine-readable`, `live-current`, or `spine` in audience-facing artifacts.

### Human-First And Agent-Ready

The artifact should read naturally to a human first and remain usable by agents through structure, not through exposed tooling language.

Use human-facing labels:

| Avoid | Use |
|---|---|
| source freshness | schedule basis / document basis |
| extraction date | schedule file reviewed / document reviewed |
| live-current | current version not confirmed / current folder not rechecked |
| schedule spine | milestone control map / schedule control map |
| operator correction | working assumption / confirmed working assumption |
| agent-ready | structured for follow-up |

Agent-ready means stable tables, dates, owner columns, and dependency fields. It does not mean telling the reader how the artifact was generated.

## Relay Safety

Before writing to Dropbox:

1. scan for local absolute paths, hostnames, SSH aliases, raw debug JSON, secrets, auth, tokens, and raw vault data
2. share summaries, not raw internals
3. if unsafe content was already placed in Dropbox, move it to:

```text
ShadowArchive/90-sensitive-sealed/dropbox-relay-retractions/<packet-id>-<timestamp>/
```

4. regenerate a sanitized packet

### Whole-Surface Relay Safety

Treat a relay packet as more than `payload.md`. Check every active synced file that helps operate it:

- packet maps, lifecycle files, return contracts, acks, and returns
- README/start-here files and root couplers
- schema/config files
- wrapper CLIs and examples
- guard lists and validation docs

Do not place real GitHub handles, account names, hostnames, local user paths, or specific identity blocklists inside Dropbox-visible tools. Use neutral labels in Dropbox and keep the real mapping in local sealed storage. If exact de-identification requires local knowledge, the local enricher/de-enricher owns it; synced tools only enforce generic leak classes.

Miss pattern to avoid: sanitizing the packet body while leaking the same identity through a route map, CLI example, validator path, or README.

## Useful Patterns

### Operator packet

```text
operator-packet/
  start-here.md
  release-checklist.md
  docs/
  diagrams/
  slides/
  evidence/grabs/
  transcript/excerpt.md
  md-mmd-evolution.json
```

Exclude raw video, raw OCR, raw metadata, and raw transcript JSON.

### Dropbox async A2A packet

```text
data/packets/<packet-id>/
  executive-summary.md
  start-here.md
  packet-map.json
  return-contract.json
  lifecycle.json
  returns/ack.md
```

### Obsidian research family

```text
Research/<topic>/
  Main Spec.md
  architecture.mmd
  lifecycle.mmd
  sequence.mmd
  md-mmd-evolution.json
  schemas/
  fixtures/
  scripts/validate-research.py
```

## When To Use References

- Use `references/astemo-ppt-agent.md` when generating Astemo PPT agent, Electron app, Obsidian plugin, or staff-meeting executive app artifacts.
- Use `references/fh4-reflash-operator-packet.md` when turning video/procedure evidence into operator packets.

## Validation Script

Use the bundled validator after creating an artifact family:

```bash
python3 /Users/sdluffy/.agents/skills/enterprise-artifact-generator/scripts/validate_enterprise_artifact.py <path> --mode operator-packet
python3 /Users/sdluffy/.agents/skills/enterprise-artifact-generator/scripts/validate_enterprise_artifact.py <path> --mode obsidian-research
python3 /Users/sdluffy/.agents/skills/enterprise-artifact-generator/scripts/validate_enterprise_artifact.py <path> --mode dropbox-packet
```

It checks JSON, MMD metadata headers, forbidden Astemo visual patterns, packet shape, and relay leak candidates.

## Crystallized From

- Session: 2026-04-21 / 2026-04-22
- Original work: FH4 PCU reflash video ingestion, operator guide, PPTX, MD/MMD operator packet; shadow-sync architecture with Obsidian/Dropbox/iCloud ownership; Astemo PPT Agent Electron/Obsidian research spec.
- Pattern extracted: enterprise artifacts need a governed family of Markdown, Mermaid, schemas, validation, and sanitized relay packets. Raw architecture/debug artifacts must be scrubbed or sealed before Dropbox relay.
- Session: 2026-04-22
- Original work: Recent Astemo meeting screenshot converted into an SOP timing cadence MMD/SVG artifact, then refined with Astemo design-system styling, JP/EN cross-region lanes, and Honda-facing support-scope framing.
- Pattern extracted: screenshot-derived enterprise artifacts should become a governed source family and should separate visible evidence, inferred cadence, internal regional coordination, and customer-safe Honda interpretation.
- Session: 2026-04-22
- Original work: FH4/FH4S full schedule artifact and Amanda B3 reply refinement after the operator flagged the output as too narrative and too synthetic.
- Pattern extracted: Astemo schedule artifacts for cross-region review should lead with full schedule control map, source date, facts, concerns, and confirmation requests, using concise JP-company internal note structure instead of narrative persuasion.
- Session: 2026-04-22
- Original work: Schedule dashboard cleanup after terms such as `source freshness`, `extraction`, and `spine` were called out as obvious tool/AI tells.
- Pattern extracted: keep artifacts human-first by using normal corporate labels while preserving agent usability through consistent tables and dependency fields.
- Session: 2026-04-22
- Original task: bidirectional Astemo A2A relay through Dropbox/GitHub with local de-identification and re-identification.
- Pattern extracted: scrub the whole synced relay surface, not only payload content; keep real account/host/person mappings in local sealed tools, and expose only neutral labels in Dropbox.
