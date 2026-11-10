---
name: code-quality-review
description: Use when strictly reviewing code for convention drift and for behavior-preserving restructurings that simplify abstractions, keep files under 1,000 lines, and remove ad-hoc conditionals.
license: MIT. LICENSE has complete terms.
metadata:
  upstream:
    repository: https://github.com/cursor/plugins
    path: cursor-team-kit/skills/thermo-nuclear-code-quality-review/SKILL.md
    commit: 6e3d2ea56d7d446b955eaae6ac4c8eef8bf504cf
---

# Code Quality Review

A code quality review pushes for restructurings that preserve behavior while deleting complexity. It holds the code to the conventions of its languages and frameworks. It specializes `Skill(reviews:review-loop)`. Run that loop with these parts of the brief:

- **Criteria:** the [code quality criteria](./prompts/criteria.md).
