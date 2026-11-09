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

Reviewers are stateless by design. The review log is the only memory between rounds.

## Workflow

1. Fill every placeholder in the [review prompt](./prompts/review-scope.md) and spawn a new (non-forked) sub-agent with the filled prompt as its whole message.
   - Fill `{{SCOPE}}` with the code that the request asks to review, such as files, a module, a feature, or a diff.
   - Fill `{{FEATURE_CONTEXT}}` with the feature's purpose.
   - Fill `{{OUT_OF_SCOPE}}` with the concerns that the request excludes. Omit the "Out of Scope" section when the request excludes none.
   - Fill `{{REVIEW_LOG}}` with the review log. Omit the "Review Log" section on the first round.
2. Sort the findings. A finding with one clear fix is an easy fix. A finding that needs a product or engineering decision is an open decision.
3. Present the findings in the reviewer's priority order. Propose each easy fix. Ask the user about each open decision. Wait for the user's answers.
4. Record each finding in the review log as accepted or rejected. Give the reason. Keep the log as free-form text.
5. Apply the easy fixes and the accepted decisions before the next round. Return to step 1 with the updated log. Stop when a round returns no finding that you accept.
