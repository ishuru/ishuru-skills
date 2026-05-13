# ishuru-skills

Agent skills for Craft Agent, Claude Code, and Codex — 25 curated skills covering dev workflow, design systems, AI agents, browser auth, visual rendering, knowledge capture, and more.

---

## Installation

### Quick Install

```bash
npx skills add https://github.com/ishuru/ishuru-skills
```

### Or via Plugin Marketplace

```bash
/plugin marketplace add ishuru/ishuru-skills
```

---

## Available Skills

### Dev Workflow

| Skill | Purpose | Key Features |
| ------- | --------- | -------------- |
| **gh-recent** | GitHub recent activity resolver | gh CLI-first, multi-account, skill creation pipeline |
| **github-triage** | Issue triage via label state machine | Classify, label, prioritize, assign |
| **setup-pre-commit** | Husky + lint-staged setup | Prettier, type checking, test hooks |
| **naming-conventions** | Universal naming rules | Auto-applies across all surfaces |
| **tdd** | Test-driven development | Red-green-refactor loop |
| **cli-creator** | Node.js CLI scaffolding | Commander, config, formatted output, tests |
| **skill-surgery-rd** | Skill audit and repair | Trigger coverage, duplicate detection, gap analysis |

### Design & UI

| Skill | Purpose | Key Features |
| ------- | --------- | -------------- |
| **design-an-interface** | Generate multiple interface designs | Parallel sub-agents, compare shapes |
| **shadcn** | shadcn/ui integration | Init, components, registries, theming |
| **pptx-agent** | PowerPoint creation and editing | python-pptx, html2pptx, template workflows |
| **remotion-render** | High-fidelity visual rendering | React → PNG/PDF, pixel-perfect |
| **algorithm-state-engine** | Algorithm visualizations | Canvas rendering, timeline playback, zero deps |

### AI & Agent Building

| Skill | Purpose | Key Features |
| ------- | --------- | -------------- |
| **langgraph-agent** | Stateful AI agent builder | LangGraph + LangChain, tool loops, multi-agent |
| **crystallize** | Turn deep work into reusable skills | Pattern extraction, SKILL.md generation |
| **write-a-skill** | Skill authoring toolkit | Frontmatter, progressive disclosure, validation |

### Browser & Auth

| Skill | Purpose | Key Features |
| ------- | --------- | -------------- |
| **browser-surface-bridge** | Authenticated data extraction | 4-layer auth bridge, Tailscale-gated, cookie export |

### Infrastructure

| Skill | Purpose | Key Features |
| ------- | --------- | -------------- |
| **cloudflare-worker-builder** | Cloudflare Workers scaffolding | Hono, Vite, D1/R2/KV bindings |
| **here-now** | Instant static web hosting | Files → URL, proxy routes, credential injection |
| **disk-guard** | Disk space emergency response | Diagnostics, safe reclaim, prevention |

### Knowledge & Communication

| Skill | Purpose | Key Features |
| ------- | --------- | -------------- |
| **intelligence-report** | Structured intelligence briefs | Mermaid, data tables, scenario matrices |
| **enterprise-artifact-generator** | Governed enterprise artifacts | MD specs, PPTX, PDF, relay packets |
| **domain-model** | Domain modeling and stress-testing | Terminology conflicts, ADR updates |
| **grill-me** | Relentless plan/design interviews | Decision tree resolution |
| **ubiquitous-language** | DDD glossary extraction | Ambiguity flags, canonical terms |
| **caveman** | Ultra-compressed communication | ~75% token reduction, full accuracy |

---

## Skill Details

### gh-recent
GitHub recent activity resolver. Resolve your latest repo, starred repo, or event from `gh` CLI.
**Modes:** `install` · `context` · `skill` · `starred` · `full` · `raw`
📄 [Guide](guides/gh-recent.md)

### github-triage
Triage GitHub issues through a label-based state machine. Classify, label, prioritize, and prepare for assignment.
📄 [Guide](guides/github-triage.md)

### setup-pre-commit
Set up Husky pre-commit hooks with lint-staged, Prettier, type checking, and tests.
📄 [Guide](guides/setup-pre-commit.md)

### naming-conventions
Universal naming rules applied automatically across all surfaces. Never ask what to name something.
📄 [Guide](guides/naming-conventions.md)

### tdd
Test-driven development with strict red-green-refactor loop. Supports any test framework.
📄 [Guide](guides/tdd.md)

### cli-creator
Generate complete Node.js CLI tools from a description. Commander routing, config management, formatted output, test scaffolding.
📄 [Guide](guides/cli-creator.md)

### skill-surgery-rd
Audit, repair, merge, and research agent skills. Trigger coverage analysis, duplicate detection, gap identification.
📄 [Guide](guides/skill-surgery-rd.md)

### design-an-interface
Generate multiple radically different interface designs for a module using parallel sub-agents.
📄 [Guide](guides/design-an-interface.md)

### shadcn
shadcn/ui — init, components, composition, custom registries, and Tailwind theming.
📄 [Guide](guides/shadcn.md)

### pptx-agent
Create, edit, and validate PowerPoint presentations. python-pptx, html2pptx, template workflows.
📄 [Guide](guides/pptx-agent.md)

### remotion-render
High-fidelity PNG/PDF exports from React/HTML compositions. Replaces Mermaid/ASCII when visual fidelity matters.
📄 [Guide](guides/remotion-render.md)

### algorithm-state-engine
Interactive algorithm visualizations with canvas rendering, timeline-based state snapshots, and playback controls. Single-file HTML, zero dependencies.
📄 [Guide](guides/algorithm-state-engine.md)

### langgraph-agent
Production-grade stateful AI agent workflows with LangGraph + LangChain. ReAct agents, tool loops, multi-agent orchestration.
📄 [Guide](guides/langgraph-agent.md)

### crystallize
Turn completed deep work into reusable skills, patterns, or documentation. Pattern extraction → SKILL.md generation.
📄 [Guide](guides/crystallize.md)

### write-a-skill
Create new agent skills with proper structure, frontmatter, progressive disclosure, and bundled resources.
📄 [Guide](guides/write-a-skill.md)

### browser-surface-bridge
Authenticated browser data extraction. 4-layer auth bridge, Tailscale-gated. Cookie export → curl, shared cookie jars, dispatched auth sessions.
📄 [Guide](guides/browser-surface-bridge.md)

### cloudflare-worker-builder
Scaffold and deploy Cloudflare Workers with Hono routing, Vite plugin, and static assets. D1/R2/KV bindings.
📄 [Guide](guides/cloudflare-worker-builder.md)

### here-now
Publish files and folders to the web instantly. Static hosting with proxy routes and server-side credential injection.
📄 [Guide](guides/here-now.md)

### disk-guard
Disk space emergency response. Quick diagnostics, safe reclaim, deep scan, prevention setup.
📄 [Guide](guides/disk-guard.md)

### intelligence-report
Structured markdown intelligence briefs with Mermaid flowcharts, data tables, scenario matrices, and blockquote takeaways.
📄 [Guide](guides/intelligence-report.md)

### enterprise-artifact-generator
Governed enterprise artifacts from raw work. Markdown specs, Mermaid diagrams, PPTX/deck packets, relay-safe packaging.
📄 [Guide](guides/enterprise-artifact-generator.md)

### domain-model
Stress-test plans against the domain model. Terminology conflicts, CONTEXT.md/ADR updates, ubiquitous language gaps.
📄 [Guide](guides/domain-model.md)

### grill-me
Relentless interview about a plan or design. Follows decision branches until all ambiguity resolved.
📄 [Guide](guides/grill-me.md)

### ubiquitous-language
DDD-style ubiquitous language glossary extraction. Ambiguity flags, canonical terms, saves to UBIQUITOUS_LANGUAGE.md.
📄 [Guide](guides/ubiquitous-language.md)

### caveman
Ultra-compressed communication mode. ~75% token reduction while preserving full technical accuracy.
📄 [Guide](guides/caveman.md)

---

## Configuration

All skills work out of the box. No configuration required.

### Optional Dependencies

| Skill | Dependency | Purpose |
|-------|-----------|---------|
| `browser-surface-bridge` | Tailscale | Security gating (required) |
| `langgraph-agent` | Python + langgraph | Agent runtime |
| `cloudflare-worker-builder` | Wrangler CLI | CF Workers deploy |
| `remotion-render` | Remotion | Rendering engine |

---

## Update

```bash
/plugin
# Navigate to Marketplaces → ishuru-skills → Update marketplace
```

---

## License

MIT License

---

**Last Updated**: 2026-05-13

- **GitHub**: [@ishuru](https://github.com/ishuru)
