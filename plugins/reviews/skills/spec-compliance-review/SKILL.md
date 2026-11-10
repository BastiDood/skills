---
name: spec-compliance-review
description: Use when auditing whether code behaves as the agreed plan or specification requires.
---

# Spec Compliance Review

A spec compliance review proves each agreed requirement against the code and flags behavior that nobody asked for. It specializes `Skill(reviews:review-loop)`. Run that loop with these parts of the brief:

- **Criteria:** the [spec compliance criteria](./prompts/criteria.md). Fill their `{{REQUIREMENTS}}` with each agreed requirement verbatim.
- **Presentation:** the [review summary template](./assets/review-summary-template.md).
