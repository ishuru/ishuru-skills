---
name: GitHub Ops
description: Use when automating gitHub branch, PR, issue, repository health, and release operations using gh and git.
icon: icon.svg
metadata:
  category: dev/github
  family: github
  lifecycle: active
  canonical_slug: github-ops
  icon_style: craft-category-glyph-v1
---

# GitHub Operations Automation

Autonomous GitHub workflow management for Shadow Lab projects.

## Purpose

Automate repetitive GitHub operations:
- Branch lifecycle management
- PR creation and monitoring
- Issue triage and tracking
- Repository health monitoring
- Release automation

## Dependencies

- GitHub CLI (`gh`) - authenticated and configured
- git - local repository operations
- jq - JSON parsing for API responses

## Core Operations

### Branch Management
```bash
# List stale branches
git branch -a | grep -v "main" | grep -v "develop"

# Delete merged branches
git branch --merged | grep -v "main" | xargs git branch -d

# Create feature branch
git checkout -b feature/$(date +%Y%m%d)-$(whoami)/{description}
```

### PR Operations
```bash
# List open PRs
gh pr list --state open

# Create PR from current branch
gh pr create --title "Title" --body "Description"

# Merge PR with checks
gh pr merge {number} --merge
```

### Issue Management
```bash
# List assigned issues
gh issue list --assignee @me

# Create issue from template
gh issue create --title "Title" --body "Description"

# Update issue status
gh issue edit {number} --add-label "in-progress"
```

### Repository Health
```bash
# Check workflow status
gh run list --workflow={name}

# List stale PRs (>7 days no activity)
gh pr list --state open --search "updated:<7 days ago"

# Check branch protection
gh api repos/{owner}/{repo}/branches/main/protection
```

## Automation Patterns

### Daily Health Check
1. Check workflow runs for failures
2. Identify stale PRs and issues
3. Report branch protection status
4. Summarize repository metrics

### Weekly Cleanup
1. Delete merged branches
2. Close stale PRs (>14 days)
3. Archive resolved issues
4. Update project boards

### Release Automation
1. Create release branch
2. Run full test suite
3. Generate changelog
4. Create GitHub release
5. Deploy to production

## Usage

### Interactive Mode
```bash
# Run health check
shadow-github-ops health

# Cleanup branches
shadow-github-ops cleanup

# Monitor PRs
shadow-github-Ops monitor-prs
```

### Autonomous Mode
```bash
# Run daily health check automatically
shadow-github-ops health --auto

# Autonomous PR triage
shadow-github-ops triage --auto
```

## Configuration

Repository-specific settings in `.github/config.json`:
```json
{
  "default_branch": "main",
  "protected_branches": ["main", "develop"],
  "stale_threshold_days": 7,
  "required_checks": ["tests", "lint", "security-scan"]
}
```

## Safety Features

- Dry-run mode for preview
- Confirmation prompts for destructive ops
- Automatic backup before major changes
- Rollback capability for automation failures


## Pipeline

```
Intent → Parse input → Execute API/tool → Structure output → Return
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Formatted result | Normal use |
| `json` | Raw JSON | Programmatic use |

## Artifact Routing

- Output in chat — no persistent artifacts unless operator requests

## Contract

- Never expose API tokens in output
- Respect rate limits

