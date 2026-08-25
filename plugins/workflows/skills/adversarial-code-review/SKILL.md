---
name: adversarial-code-review
description: Independently review completed implementation against its agreed plan or specification. Use when closing out feature work, a bug fix, or another implementation run.
---

# Adversarial Code Review

Use an independent reviewer to challenge completed changes against the agreed plan or specification, so that the final result has verified alignment and clear follow-up actions.

## Workflow

1. Spawn a new (non-forked) independent sub-agent to review the changes against the agreed plan/spec.
2. The reviewer sub-agent must be able to echo back how the current implementation works and how it aligns/deviates from the plan/spec.
3. Present the findings with a consistent verdict, deviation, and recommendation structure.
   - [Follow the review summary template.](./assets/review-summary-template.md)

The recommendations section is particularly critical because it determines the possible next phase of the work.
