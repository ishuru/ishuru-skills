# write-a-skill — Guide

## Quick Start

Ask the agent to create a new skill:

```
Create a skill for [purpose]
```

## Process

1. **Gather** — intent, scope, trigger phrases, audience
2. **Structure** — frontmatter (name, description, metadata, triggers)
3. **Write** — SKILL.md with process, rules, error handling, examples
4. **Validate** — `skill_validate` checks slug, frontmatter, content
5. **Install** — save to `~/.agents/skills/<slug>/SKILL.md`

## Frontmatter Schema

```yaml
---
name: 'Skill Name'
description: One-line trigger description
icon: icon.svg          # optional
metadata:
  category: dev/code
  family: design
  lifecycle: active
  canonical_slug: skill-name
---
```

## Validation Rules

- Slug: lowercase alphanumeric + hyphens
- Required fields: name, description
- Content must be non-empty after frontmatter
- Icon: svg/png/jpg if present
