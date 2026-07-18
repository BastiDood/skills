---
name: adversarial-code-review
description: Use when wrapping up some feature work, bug fix, or implementation run with an adversarial code review against the plan/spec.
---

# Adversarial Code Review

An adversarial code review is conducted by a reviewer sub-agent who is not the author of the code. The reviewer is given the plan/spec and must verify whether the newly introduced code diffs faithfully, exhaustively, and correctly implements it.

1. Spawn a new (non-forked) independent sub-agent to review the changes against the agreed plan/spec.
2. The reviewer sub-agent must be able to echo back how the current implementation works and how it aligns/deviates from the plan/spec.
3. Present the findings in accordance with the [review summary template](./assets/review-summary-template.md).

The recommendations section is particularly critical because it determines the possible next phase of the work.
