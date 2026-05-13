---
name: GitHub Skill
description: Use when turning any GitHub repo URL into live agent context — fetches README/SKILL.md/AGENTS.md and synthesizes a Codex skill or injects as session context. Use when user says "install <github-url>", "use <github-url>", or "read <github-url>".
icon: icon.svg
user_invocable: true
metadata:
  bashPattern: github.com|gh repo|gh api
  priority: 80
  category: dev/github
  family: github
  lifecycle: active
  canonical_slug: github-skill
  icon_style: craft-category-glyph-v1
---

# github-skill — GitHub URL as Agent Context

Convert a GitHub repo URL into a live skill or session context injection. This is the **README-as-agent-context** pattern extracted from the claw-code / clawhip / oh-my-codex ecosystem.

## Core Pattern

> A well-written GitHub README is already an agent instruction set.
> `URL → fetch context files → synthesize skill → attach + optionally install`

This is how clawhip was installed: the README said what to do, the agent read it and did it.

## Trigger Phrases

- `install https://github.com/...`
- `use https://github.com/...`
- `read https://github.com/...`
- `implement https://github.com/...`
- `attach https://github.com/...`

## Procedure

### 1. Parse the URL

```bash
# Extract owner/repo from GitHub URL
REPO="owner/reponame"
```

### 2. Fetch Context Files (in priority order)

```bash
# Try each file, use what exists
gh api "repos/$REPO/contents/SKILL.md" --jq '.content' | base64 -d > /tmp/gh-ctx-SKILL.md 2>/dev/null || true
gh api "repos/$REPO/contents/AGENTS.md" --jq '.content' | base64 -d > /tmp/gh-ctx-AGENTS.md 2>/dev/null || true
gh api "repos/$REPO/contents/AGENTS.md" --jq '.content' | base64 -d > /tmp/gh-ctx-AGENTS.md 2>/dev/null || true
gh api "repos/$REPO/readme" --jq '.content' | base64 -d > /tmp/gh-ctx-README.md 2>/dev/null
```

Priority: `SKILL.md` > `AGENTS.md` > `AGENTS.md` > `README.md`

### 3. Synthesize Skill

Read the fetched files and synthesize a Codex skill:

- **If SKILL.md exists**: use it directly as the skill body (already formatted for agents)
- **If AGENTS.md/AGENTS.md exists**: use as-is (agent-native format)
- **If README only**: extract: purpose, install steps, CLI surface, config, verification

Output format:

```markdown
---
name: <repo-name>
description: <one-line from README description field>
---

# <repo-name>

## Purpose
<extracted from README intro>

## Install
<extracted install steps>

## CLI / Usage
<extracted commands>

## Config
<extracted config schema>

## Verification
<extracted verification steps>
```

Save to: `~/.Codex/skills/<repo-name>/SKILL.md`

### 4. Attach the Skill

After writing the skill file, announce:
> "Skill attached as `<repo-name>`. Use `/skill <repo-name>` or I'll invoke it automatically when relevant."

### 5. Optionally Run Install Steps

If the README contains an install block (install.sh, npm install -g, cargo install, etc.):
- Present the install command to the user
- Ask: "Run install now?" (default: yes)
- Execute with appropriate flags (e.g., `CLAWHIP_SKIP_STAR_PROMPT=1` for clawhip)

### 6. Log to Shadow Arc

```bash
# Store in Shadow memory arc
arc_store "Attached skill from $REPO" --tags "skill,github,$(basename $REPO)"
```

Or write a brief log to `ShadowArchive/80-reports/`:

```bash
echo "$(date +%F) — github-skill: attached $REPO → ~/.Codex/skills/$(basename $REPO)/" \
  >> /Volumes/☯Duality/ShadowArchive/80-reports/github-skill-log.md
```

## Scope Decision

| Situation | Action |
|-----------|--------|
| SKILL.md exists | Use directly, no synthesis needed |
| AGENTS.md / AGENTS.md only | Use as-is |
| README only, clear install path | Synthesize skill + offer to run install |
| README only, no clear install | Synthesize context-only skill (no install step) |
| Private repo, no access | Ask user for a local clone path or PAT |
| Monorepo with multiple tools | Ask which sub-tool to focus on |

## Shadow Application

When `theleostark` repos are the source, prefer reading from local clones when available on `SHADOW` volume, since they may be private forks with richer content than the public README.

Known high-value repos for Shadow:
- `theleostark/ANE-LM` — Apple Neural Engine LLM inference (C++)
- `theleostark/autoresearch-mlx` — MLX autonomous research loops
- `theleostark/leo-config` — Leo's automation configs and knowledge base
- `theleostark/ouroboros` — self-creating agent architecture
- `instructkr/claw-code` — Rust harness runtime (reference architecture)
- `Yeachan-Heo/clawhip` — Discord notification router (installed)
- `Yeachan-Heo/oh-my-codex` — OMX session modes (`$ralph`, `$team`, `$architect`)

## OMX Mode Mapping (Shadow Equivalent)

| OMX keyword | Shadow equivalent |
|-------------|------------------|
| `$architect` | `superpowers:brainstorming` |
| `$ralph` | persistent execution agent (no direct equivalent yet) |
| `$team` | `superpowers:dispatching-parallel-agents` |
| `$plan` | `superpowers:writing-plans` |

## MCP Tools

- `kernel_query` — check if this repo/skill is already in Shadow memory
- `arc_store` — log the attachment for future retrieval
- `research_ingest` — if the README is substantive research, ingest it into the research queue
