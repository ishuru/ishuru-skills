---
name: Naming Conventions
description: 'Use when naming or renaming any artifact, session, chat, file, branch, commit, or deliverable across any surface. Apply automatically — never ask what to name something, just follow these rules. Trigger on: naming, renaming, titling, labeling, save-as, creating session names, chat titles, file names, branch names, commit messages, slide titles, email subjects.'
icon: icon.svg
metadata:
  priority: 95
  category: workflow/process
  family: naming
  lifecycle: active
  canonical_slug: naming-conventions
  icon_style: craft-category-glyph-v1
---

# Global Naming Conventions

Universal rules for naming anything across all surfaces. Apply automatically without asking.

## Core Principle

> **Every name must survive without context.**

A name seen in a list of 200 items should tell you: what it is, which program/project it belongs to, and when it was made. No decoding required.

---

## 1. Program Registry (Prefix Map)

Always lead with the program code from the registry. This is the single source of truth for program prefixes.

| Code | Program | Customer | Status | Dropbox Root |
|---|---|---|---|---|
| **FH4S** | Gen4 VA IPM (Honda CR-V HEV) | Honda | Active | `FH4S_4E0A_IPM/` |
| **FH4** | Gen4 inverter (Line 2) | Honda | Active | `FH4_IPM/` |
| **FH4L** | Gen4 VA long-range | Honda | Pre-dev | `FH4L_IPM/` |
| **XP7G** | Next-gen inverter | Honda | Cancelled | `XP7G_INV_eAxle/` |
| **XY9H** | BEV Gen2 platform | Honda | Pre-dev | `XY9H_INV_eAxle/` |
| **P703** | Ford inverter (ALDC-12) | Ford | Active | TBD |
| **P736** | Ford inverter (tariff) | Ford | Active | TBD |
| **P702** | Ford MCM (DCV/ATI) | Ford | Active | TBD |
| **Gen IV VA** | PCU cross-program rollup | Honda | Active | Use FH4S/FH4 |
| **SHADOW** | Internal tooling/infra | — | Active | N/A |

When a task spans programs, use the **primary** program code. Cross-program artifacts use `Gen IV VA` only if genuinely umbrella; otherwise bind to the specific program.

### Model Code Map (FH4S)

| Code | Region | Site | Config | Use in Names |
|---|---|---|---|---|
| 4D0A | JP | JPMRP1 | AWD baseline | Reference only — do NOT prefix US deliverables |
| 4E0A | US | USGRP1 | 2WD+AWD | Default for all US localization artifacts |
| 4F0A | US | USGRP1 | variant | Verify before using externally |

---

## 2. Surface Limits (Hard Constraints)

These are platform-enforced maximums. Never exceed them.

### Communication Surfaces

| Surface | Max | Allowed Chars | Practical Max | Notes |
|---|---|---|---|---|
| **M365 Copilot Chat title** | **50** | Unicode | 50 | Hard limit. No workaround. |
| **Craft Agents session name** | **50** | Unicode | 50 | UI limitation. |
| **Teams chat title** | **50** | Unicode | 50 | Rename via ⋯ menu. |
| **Outlook email subject** | 255 | Unicode | **80** | Mobile truncates after ~40. |
| **Teams message** | N/A | Unicode | — | No title concept. |
| **Slack channel name** | 80 | `a-z 0-9 - _` | 80 | Lowercase only. No uppercase. |
| **Slack message (broadcast)** | 40,000 | Unicode | — | Not a naming surface. |

### File Surfaces

| Surface | Max | Forbidden Chars | Notes |
|---|---|---|---|
| **macOS filename** | 255 | `:` `/` | HFS+/APFS. Colon silently breaks. |
| **Dropbox filename** | 255 | `\ / : * ? " < > \|` | Sync fails silently on invalid chars. |
| **SharePoint filename** | 255 | `\ / : * ? " < > \| # %` | Stricter than Dropbox. |
| **Git filename** | N/A | N/A | OS-level limits apply. |
| **Obsidian note** | 255 | `\ / : * ? " < > \|` | Filename = title. Keep under 80. |

### Development Surfaces

| Surface | Max | Allowed Chars | Notes |
|---|---|---|---|
| **Git branch name** | ~250 | No spaces, `..`, `~`, `^`, `:`, `\` | Practical max **60**. |
| **Git commit subject** | 72 hard | Unicode | Ideal **50**. Body after blank line. |
| **Git tag** | ~250 | No spaces, `..`, `~`, `^`, `:`, `\` | Use semver or YYMMDD. |
| **GitHub issue title** | 256 | Unicode | Practical max **80**. |
| **Linear issue title** | 256 | Unicode | Practical max **80**. |
| **GitHub PR title** | 256 | Unicode | Practical max **72**. |

### Planning/Tracking Surfaces

| Surface | Max | Notes |
|---|---|---|
| **Smartsheet sheet** | 50 | Planview limit. |
| **M365 Planner task** | 100 | |
| **Calendar event** | 255 | Practical max **60** for month view. |
| **Reminders/Notes title** | 255 | Practical max **60**. |
| **Bookmark name** | N/A | Practical max **60**. |

### Agent/Session Surfaces

| Surface | Max | Notes |
|---|---|---|
| **Craft session name** | 50 | Same as Copilot limit. |
| **Craft session labels** | ~50 each | Comma-separated in UI. |
| **Skill name** | ~50 | Must match slug from filename. |
| **Skill slug** | ~50 | `lowercase-with-hyphens` only. |

### Presentation Surfaces

| Surface | Max | Notes |
|---|---|---|
| **PowerPoint slide title** | N/A | Practical max **60**. |
| **PPTX filename** | 255 | Same as Dropbox rules. |
| **HTML page `<title>`** | N/A | Practical max **60** for browser tabs. |
| **Section header (doc)** | N/A | Practical max **60** for scanability. |

---

## 3. Compression Gradient

A single artifact may need names at multiple lengths as it crosses surfaces. The name must **compress gracefully** — losing specificity but never becoming ambiguous.

### Example: FH4S Lead Frame Feasibility Package

| Length | Surface | Name |
|---|---|---|
| 50+ chars | Dropbox file | `FH4S_LF_Redacted_Generic_Drawing_260503.html` |
| 50+ chars | Package folder | `lead-frame-localization/package-cover.md` |
| 50 chars | Copilot chat | `FH4S LF Redacted Feasibility Drawings` |
| 40 chars | Git branch | `fh4s/feat/redacted-lf-drawing` |
| 50 chars | Git commit | `fh4s: add redacted LF feasibility drawing` |
| 80 chars | Email subject | `FH4S IPM LF Feasibility – Data Package for Review 260503` |
| 60 chars | Calendar | `FH4S LF Supplier Review – Fri` |
| 27 chars | Session label | `redacted-drawing` |

### Compression Priority Order

When you must shorten, drop information in this order:

1. ~~Date~~ (if already implicit from context — e.g., today's chat)
2. ~~Descriptor adjective~~ ("Redacted" → "Red")
3. ~~Department~~ ("DE" → drop if program context makes it obvious)
4. ~~Product type~~ ("IPM" → drop if program only has one product)
5. **Never drop program code** — this is the last thing removed
6. **Never drop the core noun** — "Drawing", "Tracker", "Report" must survive

### 50-Char Compression Test

For any name that might land in a 50-char surface, verify it compresses:

```
Original:  FH4S IPM Lead Frame Redacted Feasibility Drawing  (48 chars ✓)
Compress:  FH4S LF Redacted Drawings                         (27 chars ✓)
Emergency: FH4S LF Feas                                       (14 chars ✓)
FAIL:      Redacted drawing package for supplier feasibility  (no program code)
```

---

## 4. Date Format Rules

### Standard: YYMMDD

Always **YYMMDD** — 6 digits, sorts chronologically, universally compact.

| Bad | Good |
|---|---|
| `May 3 Report` | `260503` |
| `2026-05-03 Deck` | `260503` |
| `03-May-2026` | `260503` |
| `5/3/26` | `260503` |
| `20260503` | `260503` |

### Exceptions

| Surface | Format | Reason |
|---|---|---|
| **Dropbox folder paths** | `YYYY-MM-DD` | ISO 8601 sort expected by some tools |
| **ISO documents / IATF** | `YYYY-MM-DD` | Standard mandates full ISO date |
| **Git tags (releases)** | `vYYMMDD` or semver | Tag convention |
| **Weekly artifacts** | `W##` + optional YYMMDD | `W19` or `W19_260505` |

### Date vs Version

| Situation | Use | Example |
|---|---|---|
| Recurring deliverable (weekly report) | Date: `W19 260505` | `FH4S DE Report W19 260505` |
| Sequential deliverable (tracker) | Version: `v9` | `FH4S CP Tracker v9` |
| One-time artifact (feasibility pkg) | Date: `260503` | `FH4S LF Feasibility 260503` |
| Meeting (weekly sync) | Day or date | `FH4S JP Sync Wed` or `260505` |
| Ad-hoc session/chat | Date: `260503` | `FH4S DWG Review 260503` |
| Document revision | Rev letter | `Rev A`, `Rev B` |
| Drawing revision | Rev number | `Rev 01`, `Rev 02` |

---

## 5. Abbreviation Table

Never spell out what has a code. Case matters — see the Case Rules section.

### Program & Product

| Full | Abbr | Notes |
|---|---|---|
| Intelligent Power Module | IPM | |
| Power Module | PM | **Not** Project Manager |
| Power Control Unit | PCU | |
| Inverter | INV | |
| eAxle | eAxle | camelCase, not e-axle |
| Motor Control Module | MCM | Ford P702 |
| Gen4 VA | Gen4 VA | Not "Gen IV" or "Gen4VA" |

### Lead Frame Specific

| Full | Abbr | Notes |
|---|---|---|
| Lead Frame | LF | |
| Trace | TRC | Circuit function |
| Generator | GEN | Circuit function |
| Vehicle Control Unit | VCU | Circuit function |
| Rectifier | RR | Circuit function |
| Output (polarity) | O | Prefix: O TRC, O GEN |
| Neutral (polarity) | N | Prefix: N TRC, N GEN |

### Departments & Roles

| Full | Abbr | Notes |
|---|---|---|
| Design Engineering | DE | |
| Quality Assurance | QA | |
| Supplier Quality | SQ | |
| Procurement / Buyer | BUP | From Japanese 購買 |
| Product Engineering | PE | |
| Product Manager | PMgr | **Not** PM (= Power Module) |
| Manufacturing Engineering | MFG | |
| Management | MGMT | |

### Document Types

| Full | Abbr | Notes |
|---|---|---|
| Presentation | Pres | |
| Spreadsheet | SS | |
| Tracker | Trk | |
| Schedule | Sched | |
| Drawing | DWG | |
| Package Cover | PkgCov | Was "Transmittal" — renamed 260503 per Astemo practice |
| Comparison | Comp | |
| Redacted | Red | |
| Feasibility | Feas | |
| Response | Resp | |
| Report | Rpt | |
| Meeting Notes | Notes | Don't abbreviate further |
| Change Point | CP | |
| Approval | Appr | |

### Quality / SASG

| Full | Abbr | Notes |
|---|---|---|
| Key Characteristic | KC | GD&T classification |
| Very Important Part | VP | GD&T classification |
| DFMEA | DFMEA | Don't abbreviate further |
| DRBFM | DRBFM | Don't abbreviate further |
| Control Plan | CPlan | Not CP (= Change Point) |
| PPAP | PPAP | |
| Production Verification | PV | |
| Manufacturing Readiness | MLC | Manufacturing Literacy Check |
| Design Review | DR + number | DR1, DR2 |
| Test Readiness | TR | |

### Process / Status

| Full | Abbr | Notes |
|---|---|---|
| Localization | Loc | |
| Start of Production | SOP | |
| Standup | Standup | **Don't abbreviate** |
| Kickoff | Kickoff | **Don't abbreviate** |
| Action Item | AI | |
| Bill of Materials | BOM | |
| Engineering Change Notice | ECN | |

### Calendar

| Full | Abbr | Notes |
|---|---|---|
| Monday | Mon | |
| Tuesday | Tue | |
| Wednesday | Wed | |
| Thursday | Thu | |
| Friday | Fri | |
| Weekly (numbered) | W## | W19, W23 |
| JP time | JST | |
| US Eastern | EST/EDT | |

---

## 6. Case Rules

Different surfaces expect different casing. Follow these exactly.

| Surface | Case | Example |
|---|---|---|
| Program code | **UPPER** | `FH4S`, `P703`, `XY9H` |
| Dropbox file names | **Title_Case** with underscores | `FH4S_Change_Point_Tracker_v9.xlsx` |
| Dropbox folder names | **Title_Case** with underscores | `04_Presentations/`, `03_Drawings/` |
| Git branches | **kebab-lowercase** | `fh4s/feat/redacted-lf-drawing` |
| Git commits | **lowercase imperative** | `fh4s: add redacted LF drawing` |
| Chat/session titles | **Title Case with spaces** | `FH4S LF Redacted Drawings` |
| Email subjects | **Title Case with spaces** | `FH4S IPM LF Feasibility – Review 260503` |
| Calendar events | **Title Case with spaces** | `FH4S JP-US Weekly Sync – Wed` |
| Slack channels | **kebab-lowercase** | `fh4s-de-standup` |
| Smartsheet sheets | **Title Case with spaces** | `FH4S DE Activity Schedule` |
| Skill slugs | **kebab-lowercase** | `naming-conventions` |
| Agent labels | **kebab-lowercase** | `fh4s`, `redacted-drawing` |
| Obsidian notes | **Title Case with spaces** | `FH4S DE Standup W19` |

---

## 7. Surface-Specific Templates

### M365 Copilot Chat / Craft Session (≤50 chars)

Pattern: `{PROGRAM} {TOPIC} {DATE}`

```
FH4S LF Redacted Drawings           ← 27 ✓
FH4S CP Tracker Update W19          ← 27 ✓
FH4S MLC Honda Resp 260503          ← 27 ✓
FH4S DE Standup 260503              ← 21 ✓
FH4S VCU Chip Failure Brief         ← 28 ✓
FH4S SASG Quality Forms             ← 23 ✓
P703 INV DWG Review 260415          ← 26 ✓
XY9H BEV Busbar Feasibility         ← 28 ✓
FH4L IPM Loc Planning 260601        ← 27 ✓
```

### Teams Chat (≤50 chars)

Pattern: `{PROGRAM} {COUNTERPART} {PURPOSE}`

```
FH4S JP Sync W19                    ← 17 ✓
FH4S ↔ Hosoya-san DWG               ← 20 ✓
FH4S DE Team Standup                 ← 20 ✓
P703 Ford Delivery Review            ← 27 ✓
```

### Email Subject (≤80 chars practical)

Pattern: `{PROGRAM} {TOPIC} – {ACTION/STATUS} {DATE}`

```
FH4S IPM LF Feasibility – Redacted Package for Review 260503
FH4S CP Tracker v9 – 14 Open Items – DE Update Required
FH4S VCU Chip Failure – P0 Status Update – Action Needed by 260505
P703 Ford INV – DR2 Gate Package Ready
```

### Dropbox File Name (≤255 chars)

Pattern: `{PROGRAM}_{DESCRIPTOR}_{VERSION|DATE}.{ext}`

```
FH4S_Change_Point_Tracker_v9_260402.xlsx
FH4S_LF_Redacted_Generic_Drawing_260503.html
FH4S_MLC_Honda_Response_260211.pptx
FH4S_DE_Weekly_Report_W18_260501.pptx
FH4S_PV_Schedule_260331.xlsx
FH4S_DWG_Plan_v2_260225.xlsx
P703_INV_Delivery_Package_260415.zip
```

### Dropbox Folder Path

Pattern: `{PROGRAM}_{MODEL}_{PRODUCT}/{SECTION}_{NAME}/`

```
FH4S_4E0A_IPM/04_Presentations/lead-frame-localization/
FH4S_4E0A_IPM/03_Drawings/
FH4S_4E0A_IPM/02_Change_Point_Tracker/
XY9H_INV_eAxle/03_Drawings/
P703_INV/04_Presentations/
```

Folder numbers are canonical (from astemo-project-schema): `01_SASG_Documents/` through `10_Change_Points_History/` plus `90_Astemo_Config/`.

### Git Branch (≤60 chars practical)

Pattern: `{program}/{type}/{short-description}`

Types: `feat`, `fix`, `refactor`, `docs`, `chore`, `skill`, `test`

```
fh4s/feat/redacted-lf-drawing
fh4s/fix/tracker-v9-columns
xp7g/refactor/drawing-query
shadow/skill/naming-conventions
p703/docs/delivery-package
```

### Git Commit Subject (≤50 chars ideal, 72 hard)

Pattern: `{scope}: {imperative verb} {description}`

```
fh4s: add redacted LF feasibility drawing
tracker: sync CP v9 to smartsheet
naming: add global convention skill
p703: prepare DR2 gate package
```

### Calendar Event (≤60 chars practical)

Pattern: `{PROGRAM} {MEETING TYPE} – {DAY|DATE}`

```
FH4S JP-US Weekly Sync – Wed
FH4S DR2 Gate Review – Thu
FH4S DE Standup – Mon
P703 Ford SQ Audit – Fri 260510
FH4S MLC Resp Deadline – Mon 260511
```

### Smartsheet Sheet (≤50 chars)

```
FH4S DE Activity Schedule
FH4S CP Tracker Master
P703 INV Delivery Timeline
```

### Agent Session Title (≤50 chars)

Pattern: `{PROGRAM} {TOPIC} — {MODEL}` or `{TOPIC} — {SURFACE}`

```
FH4S LF Redacted — GLM-5.1
FH4S CP Update — Craft
Shadow Feed Digest — Opus
XP7G DWG Query — Flash
P703 INV Planning — Craft
```

### Skill Name / Slug

Pattern: `kebab-lowercase`, descriptive, no program prefix

```
naming-conventions
fh4s-context
fh4s-drawing-query
astemo-xlsx
shadow-trmnl
```

---

## 8. Bilingual Considerations (JP ↔ US)

### In File Names

- Use **English only** in file names. Japanese characters can cause encoding issues on Windows (work laptop) and SharePoint.
- If a Japanese term is the standard name, romanize it: `KISO` not `規格`, `DRBFM` not `デザインレビュー`.

### In Email Subjects (JP-bound)

- **English subject line** is always acceptable and preferred for cross-cultural communication.
- If bilingual is needed: `FH4S IPM CP Tracker v9 – 設計変更点管理表` (English first, Japanese after em-dash).
- Never exceed 80 chars total for bilingual subjects — JP characters consume more visual width.

### In Chat Titles

- Always English. M365 Copilot and Teams sort alphabetically; Japanese characters sort unpredictably.
- Japanese colleague names in titles are OK: `FH4S ↔ Hosoya-san DWG` ✓

### In Meeting Notes

- English section headers.
- Japanese terms preserved inline when they are standard vocabulary: `KISO 規格`, `DRBFM`, `BUP (購買)`.
- Always provide English gloss for any Japanese term on first use.

---

## 9. Confidentiality Tiering in Names

### Classification Markers

| Tier | Marker | Where | Example |
|---|---|---|---|
| **Internal** | (none) | Default | `FH4S DE Standup W19` |
| **Controlled** | No marker in name | Metadata/footer | `FH4S LF Redacted Drawings` |
| **Customer-facing** | No marker | Footer/classification block | `FH4S MLC Honda Resp 260211` |
| **Redacted** | `Red` or `Redacted` in name | Title + watermark | `FH4S LF Red Generic DWG` |

### Redaction Naming

When creating redacted/sanitized versions of proprietary documents:

| Original | Redacted |
|---|---|
| `030E5-M6737-011_LEAD FRAME O TRC.smart.json` | `LF_O_TRC_Redacted` |
| `FH4S_4E0A_IPM_Change_Point_Tracker.xlsx` | `FH4S CP Trk Redacted` |
| Full drawing number | Generic functional name |

**Redacted names must NOT contain:**
- Drawing numbers (`030E5-xxxx-xxx`)
- Part numbers (`6640-xxxx-xxxx`)
- ECN numbers (`E-P-xxxxxxx`)
- Honda reference codes (`3HAxx`)
- Personnel identifiers (NAKA, MURA, IWATA, etc.)
- Customer-internal spec numbers (unless required for supplier qualification)

**Redacted names MUST contain:**
- Program code (FH4S) — the supplier needs to know the program
- Functional description (LF, O TRC, etc.)
- The word "Redacted" or "Red" to signal the redaction

---

## 10. What NOT to Include in Names

| Exclude | Why |
|---|---|
| Personnel names (JP or US) | Changes hands, not universally known |
| Internal tool/agent names | Not meaningful outside agent context |
| Version numbers in chat titles | Use dates (v9 → date suffix) |
| Emoji in formal documents | Breaks some systems, unprofessional |
| "Draft" / "Final" | Use status metadata or folder, not title |
| Parenthetical asides | Waste chars, break sorting |
| Japanese characters in filenames | Encoding issues on Windows/SharePoint |
| Model codes in chat titles | `4E0A` is noise for a 50-char limit; use in files only |
| "Update" / "New" / "Latest" | Temporal labels rot instantly |

---

## 11. Naming Validation Checklist

After naming any artifact, verify all that apply:

```
✓ Under surface character limit
✓ Starts with program code (FH4S, P703, etc.)
✓ Contains date (YYMMDD) or version where applicable
✓ Uses abbreviations from table (not invented ones)
✓ Correct case for surface (UPPER/Title/kebab/lower)
✓ No forbidden characters for that surface
✓ No proprietary identifiers (if redacted)
✓ Sorts correctly with siblings (program prefix ensures this)
✓ Parseable without creator context
✓ Compresses gracefully to 50 chars without losing program code or core noun
```

---

## 12. JP-Originated File Immunity

Files created outside US DE (JP DE, Honda, JP Purchasing, any Astemo JP system) are **never renamed**, regardless of naming convention violations. They carry traceability value in their original names.

**Markers of JP origin:**
- Prefixed with `(Ho)_` or `Ho `
- SASG template forms (`SASG_X10-02A_*`)
- Japanese characters in filename
- Spaces, non-standard separators
- Files from SharePoint, email attachments, Laserfiche

If metadata enrichment is needed, add a companion `.meta.json` instead of renaming.

## 13. Localization Package Convention

All supplier-facing localization packages follow a fixed kebab-case structure:

```
{component}-localization/
├── feasibility-checklist.md
├── material-card.md
├── package-cover.md          ← was transmittal-cover.md
├── redacted-{component}-generic.html
├── supplier-email-drafts.md
└── [optional] SOURCE-EXTRACTION-INTERNAL.md
```

Folder and files: **kebab-case, lowercase**. This is the one category where lowercase kebab is canonical (not PascalCase+underscore).

## 14. Smart Drawing Sidecar

Smart drawing JSON sidecars: `{DrawingNumber}_{PartName}.smart.json`

- L2 classified, never shared externally
- `.smart.json` extension is the Astemo agent convention for structured drawing extraction
- Drawing numbers are Honda KISO format: `{ProjectPrefix}-{PartSerial}-{Revision}`
- In redacted/supplier files, replace drawing number with functional name: `redacted-upper-cover-generic.html`

## 15. Document ID Format

Internal document IDs: `F4S-{ComponentCode}-{DocType}-{Seq}`

| Component Code | Meaning |
|---------------|----------|
| `LF` | Lead Frame |
| `HS` | Heat Sink |
| `BB` | Busbar |
| `PM` | Plastic Molded |
| `DC` | Die-Cast |
| `SEC` | Security Protocol |
| `PT` | Press Terminal |
| `FT` | Fastener |

Examples: `F4S-LF-FEAS-001`, `F4S-DC-FEAS-001`, `F4S-SEC-001`

## 16. Language Suffix

When bilingual versions exist, append language code before extension:

| Suffix | Meaning |
|--------|----------|
| `_JPEN` | Japanese + English bilingual |
| `_EN` | English only |
| `_JP` | Japanese only |

Example: `FH4S_Conformal_Coating_Assessment_2026-04-22_Rev15_JPEN_Draft_Awaiting_Feedback.pptx`

---

## Crystallized From

- Session: 2026-05-03 (Craft Agents, FH4S LF Redacted Drawing)
- Trigger: Operator asked to rename M365 Copilot chat, hit 50-char limit. Requested global naming convention covering all surface limitations.
- Pattern: Every surface has a different character limit and character set restriction. A single naming convention must compress gracefully from email subjects (80 chars) down to Copilot chat titles (50 chars) while remaining parseable.

- Deepening: 2026-05-03 (same session)
- Trigger: Operator said "Deeper" — skill lacked program registry cross-reference, folder path conventions, bilingual rules, confidentiality tiers, compression gradient analysis, case rules, and sufficient surface coverage.
- Added: Program registry map (10 programs), 40+ surface limit entries, compression gradient with priority order, bilingual JP/US rules, confidentiality tiering, case rules per surface, expanded abbreviation table (60+ entries), folder path templates from astemo-project-schema.
