---
name: Template Surface Router
description: Use when the user asks for templates, starter files, reusable formats, boilerplates, base documents, app/service scaffolds, or a template library across documents, apps, dashboards, plugins, cloud services, M365/Astemo artifacts, Obsidian notes, GitHub issues, or publishing surfaces.
icon: icon.svg
metadata:
  depends_on: [pptx-agent, astemo-xlsx, astemo-org-templates, astemo-work-context, storage-ops, shadcn, here-now]
  category: workflow/template
  family: templates
  lifecycle: active
  canonical_slug: template-surface-router
  icon_style: craft-category-glyph-v1
---

# Template Surface Router

Canonical router for “what template should I use?” across documents, apps, and services.

## First classify the surface

Ask or infer:

1. **Audience:** personal, operator-only, Astemo/internal, customer-facing, public web.
2. **Surface:** PPTX, XLSX, DOCX, PDF, Markdown, HTML, app, service/API, plugin, message, issue, dashboard.
3. **System of record:** Dropbox, M365/SharePoint/OneDrive, repo, Obsidian, Smartsheet, GitHub, TRMNL, web host.
4. **Template state:** canonical available, local fallback available, existing artifact to copy, or missing template to create.

## Routing matrix

| Surface / intent | Use these skills |
|---|---|
| Astemo template question | `astemo`, `astemo-work-context`, then lane below |
| Astemo PPTX/decks | `pptx-agent`, `pptx-agent/astemo`, `astemo-org-templates`, `astemo-presentation-prep-relay`, `astemo-template-fallback` |
| Generic PPTX/decks/slides | `pptx`, `pptx-agent`, `web-slide-studio`, `pptx-visual-validation` |
| Excel/workbooks/trackers | `xlsx`, `astemo-xlsx`, `astemo-org-templates`, `astemo-smartsheet`, `fh4s-sasg-forms` |
| Word/professional docs | `docx`, `doc-coauthoring`, `internal-comms`, `enterprise-artifact-generator`, `astemo-template-fallback` |
| PDF/print packets | `pdf`, `enterprise-artifact-generator` |
| Markdown specs/reports | `doc-coauthoring`, `intelligence-report`, `md-evolution`, `enterprise-artifact-generator` |
| Obsidian notes/bases/canvases | `obsidian-markdown`, `obsidian-cli`, `obsidian-bases`, `json-canvas` |
| HTML pages/prototypes/dashboards | `frontend-design`, `huashu-design`, `data-dashboard`, `web-artifacts-builder`, relevant design-system skill |
| Publishable web artifacts | `here-now`, `web-artifacts-builder`, `cloud-drive-routing` for storage/source routing |
| Cloudflare apps/services | `cloudflare-worker-builder` |
| MCP/API integrations | `mcp-builder`, `plugin-creator`, `ai-sdk-core` if LLM-backed |
| TRMNL/e-paper plugins | `trmnl-plugin-dev`, `shadow-trmnl` |
| shadcn/UI registries | `shadcn`, `web-artifacts-builder`, `frontend-design` |
| GitHub PRDs/issues/refactor plans | `to-prd`, `to-issues`, `github-triage`, `triage-issue`, `request-refactor-plan` |
| Email/Teams messages | `outlook-draft-guardrails`, `outlook-headless-workflow`, `teams-rich-compose`, `sabarish-communication-style`, `astemo-colleague-attunement` |
| X/Twitter content | `x-content-lab`, `x-growth`, `x-operator`, `x-claims` |
| Storage/project folder templates | `cloud-drive-routing`, `astemo-project-schema`, `storage-ops` |

## Inventory helper

```bash
node /Users/sdluffy/.agents/skills/template-surface-router/scripts/inventory-templates.mjs --out=data/template-surface/template-inventory.json --max-depth=5
node /Users/sdluffy/.agents/skills/template-surface-router/scripts/curate-template-registry.mjs --inventory=data/template-surface/template-inventory.json --out=data/template-surface/template-registry.json --markdown-out=data/template-surface/template-registry.md
```

The inventory helper scans known local template roots and skill asset/template folders. It does not authenticate to cloud APIs or print file contents. The registry helper adds heuristic audience, boundary, status, system-of-record, owning-skill, and route labels for curation.

## Template resolution order

1. **Canonical template** from active skill or source-of-truth folder.
2. **Local fallback** such as `~/Templates/Astemo/` or a skill `assets/` template.
3. **Existing artifact copy-reuse**: copy a good prior file and clear content while preserving styling/layout/master data.
4. **Generate a new template** only when no canonical/fallback/prior artifact exists.

## Copy-reuse rule

For Office files, never build from blank when a styled base exists.

```text
find base → copy with metadata → clear volatile content → preserve masters/styles → fill new content → visually validate
```

## Gaps to create when missing

If no skill owns a template lane, create one only after inventory. Good candidates:

- service-specific template packs for recurring APIs
- app-specific shadcn/page scaffolds
- M365/SharePoint library template maps
- personal life-admin document templates
- operator dashboard template packs

## Safety

- Do not print tokens/credentials inside template skills.
- Do not expose SHADOW internals in customer/work-visible templates.
- For time-sensitive service templates, research current docs before baking version numbers into a template.

## Crystallized From

- Session: 2026-05-02 follow-up from deprecated `astemo-pptx-templates` asking for template skills across all documents/apps/services used.
- Pattern: template requests need a router across artifact surfaces, not one PPTX-only lane.
