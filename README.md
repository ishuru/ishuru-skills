# ishuru-skills

Agent skills for Craft Agent, Claude Code, and Codex — gh-driven workflow, skill authoring, browser auth bridges, visual rendering, and AI agent graphs.

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

### gh-recent

**GitHub Recent Activity Resolver** — Resolve your latest GitHub repo, starred repo, or event from `gh` CLI, then turn it into a skill, fetch context, or summarize.

**Trigger when**: `@github recent`, `gh-recent`, `shadow-extract GitHub`, `re-skill GitHub`, or asking to create a skill from recent GitHub activity.

**Workflow:**
1. `gh api user` → resolve account
2. `gh api users/$viewer/events` → find newest concrete event
3. Route to: `install` (create skill), `context` (fetch files), `starred` (list stars), `full` (all activity)

**Modes:** `install` · `context` · `skill` · `starred` · `full` · `raw`

📄 **Detailed Guide**: [guides/gh-recent.md](guides/gh-recent.md)

---

### write-a-skill

**Skill Authoring Toolkit** — Create new agent skills with proper structure, progressive disclosure, frontmatter, and bundled resources. Meta-skill for building skills.

**Trigger when**: Creating, writing, or building a new agent skill.

**Workflow:**
1. Gather intent and scope
2. Generate frontmatter (name, description, metadata, triggers)
3. Write structured SKILL.md with process, rules, error handling
4. Validate with `skill_validate`

📄 **Detailed Guide**: [guides/write-a-skill.md](guides/write-a-skill.md)

---

### browser-surface-bridge

**Authenticated Browser Data Access** — Invisible authenticated data extraction for agent workflows. Cookie export → curl, shared cookie jars, dispatched auth sessions. All gated on active Tailscale tailnet.

**Trigger when**: `outlook search`, `sharepoint search`, `M365 search`, `browser bridge`, `auth node`, `browser auth`, `cookie sync`, `authenticated scrape`, `login wall`.

**Layers:**
1. Cookie export → curl (zero browser)
2. Shared cookie jar via browser-auth-sync (zero focus theft)
3. Dispatched auth sessions
4. AppleScript JS injection (Safari safe, Chrome gated)

📄 **Detailed Guide**: [guides/browser-surface-bridge.md](guides/browser-surface-bridge.md)

---

### remotion-render

**High-Fidelity Visual Renderer** — Generate pixel-perfect PNG/PDF exports from React/HTML compositions using Remotion. Replaces Mermaid/ASCII when the document needs better visual fidelity.

**Trigger when**: `remotion render`, `render frame`, `typora diagram`, `high-fidelity diagram`, `pixel-perfect export`, or when Mermaid/ASCII isn't expressive enough.

**Output:** PNG stills, PDF exports from `<Composition>` + `renderStillOnWeb()`

📄 **Detailed Guide**: [guides/remotion-render.md](guides/remotion-render.md)

---

### langgraph-agent

**LangGraph Agent Builder** — Build production-grade stateful AI agent workflows using LangGraph (graph orchestration) + LangChain (LLM abstraction). Supports ReAct agents, tool-calling loops, multi-agent orchestration, and conditional routing.

**Trigger when**: LangGraph, LangChain, agent graphs, stateful agents, agentic workflows, or building AI agents with Python tools/memory/multi-step reasoning.

**Covers:** State machines, tool nodes, conditional edges, checkpointing, streaming, human-in-the-loop

📄 **Detailed Guide**: [guides/langgraph-agent.md](guides/langgraph-agent.md)

---

## Skill Overview

| Skill | Purpose | Key Features |
| ------- | --------- | -------------- |
| **gh-recent** | Resolve recent GitHub activity | gh CLI-first, multi-account, skill creation pipeline |
| **write-a-skill** | Author new agent skills | Frontmatter, progressive disclosure, validation |
| **browser-surface-bridge** | Authenticated data extraction | 4-layer auth bridge, Tailscale-gated, cookie export |
| **remotion-render** | High-fidelity visual rendering | React→PNG/PDF, pixel-perfect, replaces Mermaid |
| **langgraph-agent** | Build stateful AI agents | LangGraph + LangChain, tool loops, multi-agent |

---

## Configuration

All skills work out of the box. No configuration required.

### Optional: Tailscale

For `browser-surface-bridge`, an active Tailscale tailnet is required for security gating.

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
