---
name: adversarial-diff-review
description: Use when adversarially reviewing a completed diff for specification compliance, code quality, and performance before closing out feature work.
---

# Adversarial Diff Review

Narrow criteria keep each review focused. One review's refactor can remove a whole class of another review's findings. For example, a performance or cleanliness refactor can eliminate a class of bugs.

An adversarial diff review specializes `Skill(reviews:review-loop)`. Run that loop once with these parts of the brief:

- **Criteria:** three criteria sets. Take the "Criteria" and "Feature Context" parts of `Skill(reviews:spec-compliance-review)`, `Skill(reviews:code-quality-review)`, and `Skill(reviews:performance-review)`. Run one loop for all three sets, not one loop per skill.
- **Scope:** the completed diff.
- **Excluded Criteria:** for each set, the criteria of the other two sets.
- **Presentation:** the [review summary template](./assets/review-summary-template.md).
