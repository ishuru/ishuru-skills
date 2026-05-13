---
name: To Prd
description: Use when turning the current conversation context into a PRD and submit it as a GitHub issue. Use when user wants to create a PRD from the current context.
icon: icon.svg
metadata:
  category: memory/context
  family: memory
  lifecycle: active
  canonical_slug: to-prd
  icon_style: craft-category-glyph-v1
---

This skill takes the current conversation context and codebase understanding and produces a PRD. Do NOT interview the user — just synthesize what you already know.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already.

2. Sketch out the major modules you will need to build or modify to complete the implementation. Actively look for opportunities to extract deep modules that can be tested in isolation.

A deep module (as opposed to a shallow module) is one which encapsulates a lot of functionality in a simple, testable interface which rarely changes.

Check with the user that these modules match their expectations. Check with the user which modules they want tests written for.

3. Write the PRD using the template below and submit it as a GitHub issue.

<prd-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this PRD.

## Further Notes

Any further notes about the feature.

</prd-template>

## Pipeline

```
Conversation context → Extract requirements → Structure PRD → File as issue
```

1. Scan conversation for requirements, constraints, and decisions
2. Structure into PRD template (problem, users, requirements, success criteria)
3. File as GitHub issue with `prd` label

## Modes

| Mode | Output | When |
|------|--------|------|
| `full` (default) | Complete PRD | Turning discussion into spec |
| `lite` | Problem + requirements only | Quick spec capture |

## Artifact Routing

- PRD filed as GitHub issue with `prd` label
- Supporting analysis: `ShadowArchive/80-reports/`

## Contract

- Separate verified requirements from assumptions
- Mark uncertain items explicitly
- Include success criteria that are testable
