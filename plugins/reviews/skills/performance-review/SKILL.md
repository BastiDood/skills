---
name: performance-review
description: Use when reviewing code for performance bottlenecks and anti-patterns, such as repeated work, poor complexity, and serialized or chatty I/O.
---

# Performance Review

A performance review finds work that the program does without need. It argues each finding from its cost at realistic input sizes and call frequencies. It specializes `Skill(reviews:review-loop)`. Run that loop with these parts of the brief:

- **Criteria:** the [performance criteria](./prompts/criteria.md).
- **Feature Context:** include the expected data sizes and call frequencies. The reviewer argues every cost from them.
