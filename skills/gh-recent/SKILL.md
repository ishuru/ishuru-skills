---
name: 'GitHub Recent'
description: Use when the user says @github recent, gh-recent, recent GitHub, latest starred repo, shadow-extract GitHub, re-skill GitHub, or asks to create/use a skill from their most recent GitHub activity using gh CLI rather than browser history.
metadata:
  category: dev/github
  family: github
  lifecycle: active
  canonical_slug: gh-recent
  icon_style: craft-category-glyph-v1
---

# Gh Recent

## Core rule

For `@github recent`, `shadow-extract GitHub`, or `re-skill GitHub`, use `gh` CLI first. Do not infer from Chrome history, Downloads, current repo, or web search unless `gh` is unavailable or the user explicitly asks for browser recent.

When invoked as a re-skill, resolve the recent repo/event first, then report the reusable delta: trigger fix, fallback order, install/use pattern, or residual gap.

## Resolve recent repo

1. Check active account:

```bash
gh api user --jq .login
```

2. List recent activity:

```bash
viewer=$(gh api user --jq .login)
gh api "users/$viewer/events?per_page=50" \
  --jq '.[] | select(.repo.name) | [.created_at,.type,.repo.name,(.payload.action//""),(.payload.ref//""),(.payload.issue.html_url//.payload.pull_request.html_url//"")] | @tsv'
```

3. Prefer the newest concrete repo event. For `WatchEvent` with `started`, interpret as latest starred repo. If multiple events happen at nearly the same timestamp, list candidates and ask.

4. For starred-specific recent, use star timestamps:

```bash
gh api -H 'Accept: application/vnd.github.star+json' \
  '/user/starred?sort=created&direction=desc&per_page=20' \
  --jq '.[] | [.starred_at,.repo.full_name,.repo.description,.repo.html_url] | @tsv'
```

## Turn repo into a skill

Use the GitHub Skill workflow after resolving the repo from `gh`:

- Fetch in priority order: `SKILL.md`, `AGENTS.md`, `CLAUDE.md`, `README.md`.
- If no agent-native file exists, synthesize a concise skill from README plus important subdirectory docs.
- Save to `/Users/sdluffy/.agents/skills/<repo-slug>/SKILL.md` unless the user specifies another skill root.
- Validate with `mcp__session__skill_validate`.
- Use the new skill immediately: run one repo-specific inspection or summarize actionable usage from the source files.

## Repo context fetch snippet

```bash
repo='OWNER/REPO'
work="/tmp/gh-${repo//\//-}"
rm -rf "$work" && mkdir -p "$work"
cd "$work"
for f in SKILL.md AGENTS.md CLAUDE.md README.md; do
  gh api "repos/$repo/contents/$f" --jq '.content' 2>/dev/null | base64 -d > "$f" 2>/dev/null || true
done
find . -maxdepth 1 -type f -size +0 -print
```

## Output contract

Always include:

```text
Resolved via gh: <repo> — <event type/action/time>
Action: <summarized/opened/installed/queued/etc.>
Skill: <path when created/updated, else none>
Re-skill delta: <none | trigger/fallback/workflow patch>
Validation: <passed/failed/not run>
Used it: <one concrete command/result from the repo when skill created>
```

## Pipeline

```
Input (“@github recent” / “gh-recent” / “shadow-extract GitHub” / “re-skill GitHub”)
  ↓
Resolve Recent Activity (gh api user events → starred repos → push/PR/issues)
  ↓
Identify Target Repo (newest concrete event, or starred if WatchEvent)
  ↓
Action Routing
  ├── Skill creation → fetch SKILL.md/AGENTS.md/README → synthesize → validate → use
  ├── Context fetch → download key files to /tmp → summarize
  └── Direct use → run one repo-specific command
  ↓
Artifact Routing
  ├── /Users/sdluffy/.agents/skills/<repo-slug>/SKILL.md   (new skill if created)
  ├── /tmp/gh-<owner>-<repo>/                              (fetched context files)
  └── Chat output (compact summary + action rail)
```

## Modes

### Default: Recent Repo Summary
- One-paragraph summary: repo name, event type, time, description.
- Action rail: `install` · `context` · `skill` · `starred`

### install
- Resolve recent repo → fetch agent files → create skill → validate → run one source-grounded action.

### context
- Fetch SKILL.md/AGENTS.md/CLAUDE.md/README.md to `/tmp/gh-<repo>/` without creating a skill.

### skill
- Same as install but explicitly for skill creation workflow.

### starred
- List recent starred repos with timestamps: `gh api /user/starred?sort=created&direction=desc`.

### full
- Complete activity log with all event types, timestamps, and action recommendations.

### raw
- Unprocessed `gh api` JSON output. No analysis.

## Artifact Routing

| Artifact | Path | Purpose |
|----------|------|----------|
| New skill | `/Users/sdluffy/.agents/skills/<repo-slug>/SKILL.md` | Installed skill for reuse |
| Fetched context | `/tmp/gh-<owner>-<repo>/` | Temporary repo file cache |

Skill files are persistent. `/tmp/` context is transient — do not depend on it across sessions.

## Fallback Chain

1. **Primary: `gh` CLI API** — `gh api user` + `users/$viewer/events` for activity, `/user/starred` for stars.
2. **Browser Surface Bridge** — Check GitHub in authenticated Chrome if `gh` is unavailable.
3. **Web search** — Search for the repo name/description when `gh` is unavailable and browser extraction fails.
4. **Operator description** — Ask operator to specify the repo when all automated methods fail.

When falling back: `⚠ Resolved via web search (gh CLI unavailable).`

## Prerequisites

- `gh` CLI installed and authenticated (`gh auth status`)
- GitHub account with activity in the target timeframe
- `web_fetch` / `defuddle` (for fetching repo files via API)
- `mcp__session__skill_validate` (for validating created skills)

## Error Handling

| Failure | Recovery |
|---------|----------|
| `gh` not authenticated | Run `gh auth status`; report issue; fall back to browser/web search |
| No recent activity | Report empty activity; ask if user means a specific repo |
| Repo has no README or agent files | Synthesize skill from subdirectory docs; note limited source material |
| Skill creation fails validation | Report validation errors; fix and re-validate |
| Multiple repos at same timestamp | List candidates with descriptions; ask operator to choose |
| Rate limited by GitHub API | Use `gh api` with pagination; max 100 events per call; wait between calls |

## Contract

- **`gh` CLI first:** Do not check browser history, Downloads, or web search before `gh api`. CLI is authoritative.
- **No assumption about “recent”:** Resolve from GitHub API activity, not local filesystem state.
- **Skill validation required:** Every created skill must pass `mcp__session__skill_validate` before being considered complete.
- **Use after create:** When a skill is created, run one concrete source-grounded action immediately. Do not create and abandon.
| **Ambiguity disclosure:** When events disagree or multiple candidates exist, show all options and ask. Do not pick silently.
- **No write actions to repos:** This skill reads repos and creates local skills. It does not push, PR, or modify remote repos.
- **SHADOW separation:** Keep SHADOW-internal references out of created skill files unless the repo itself is SHADOW infrastructure.
- **Transient context cleanup:** `/tmp/gh-*` directories are session-scoped. Do not expect them to persist.

## Common mistakes

- Mistake: Checking browser history first. Fix: `gh api user` and user events first.
- Mistake: Assuming the latest visited GitHub page is the desired repo. Fix: resolve from GitHub activity.
- Mistake: Creating a skill but not using it. Fix: perform one source-grounded action after validation.
- Mistake: Hiding ambiguity. Fix: if events disagree, show candidates and ask.
