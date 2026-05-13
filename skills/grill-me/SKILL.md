---
name: Grill Me
description: 'Use when the user needs: Interview the user relentlessly about a plan or design until reaching shared understanding, resolving each branch of the decision tree. Use when user wants to stress-test a plan, get grilled on their design, or mentions "grill me".'
icon: icon.svg
metadata:
  category: dev/workflow
  family: design
  lifecycle: active
  canonical_slug: grill-me
  icon_style: craft-category-glyph-v1
---

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time.

If a question can be answered by exploring the codebase, explore the codebase instead.

## Pipeline

```
Plan/design → Identify decision points → For each branch: challenge assumption → Force resolution → Record decision
```

1. Parse the operator's plan or design
2. Identify every assumption, implicit decision, and unexplored branch
3. For each branch: challenge with "what if X goes wrong?"
4. Do not stop until the operator has resolved every branch
5. Summarize resolved decisions

## Modes

| Mode | Intensity | When |
|------|-----------|------|
| `grill` (default) | Full decision tree interrogation | Before committing to a plan |
| `quick` | Top 3 risks only | Fast sanity check |
| `adversarial` | Worst-case only | Stress-testing for failure modes |

## Contract

- Do not accept vague answers — push for specific, falsifiable commitments
- Record all decisions made during grilling
- Stop only when operator explicitly says to stop or all branches resolved
