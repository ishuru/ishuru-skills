# FH4 Reflash Operator Packet Pattern

Use when turning long procedure video/audio evidence into a safe operator packet.

## Source Pattern

1. preserve original in managed bundle
2. extract Parakeet transcript
3. sample frames and OCR as evidence
4. write operator guide
5. write deep extraction
6. build PPTX guide
7. create sanitized `operator-packet`
8. add MMD procedure diagrams
9. add MD/MMD evolution manifest
10. validate exclusions

## Operator Packet Shape

```text
operator-packet/
  start-here.md
  release-checklist.md
  docs/operator-guide.md
  docs/deep-extraction.md
  docs/evidence-map.md
  diagrams/*.mmd
  diagrams/rendered/*.svg|*.png
  slides/*.pptx
  evidence/grabs/*.jpg
  transcript/parakeet-excerpt.md
  md-mmd-evolution.json
```

## Exclude

- original raw video
- raw iPhone/location metadata
- ffprobe metadata
- raw OCR dumps
- full raw transcript JSON
- secrets/auth/debug data

## Required Diagrams

- procedure flow
- lifecycle state
- safety gates

## Current Example

```text
shadow-agent-ent/projects/fh4/inputs/software-flashing/2026-04-21-keywriting-procedure/operator-packet/
```

