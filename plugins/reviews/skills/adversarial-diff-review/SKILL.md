---
name: adversarial-diff-review
description: Use when adversarially reviewing a completed diff for specification compliance, code quality, and performance before closing out feature work.
---

# Adversarial Diff Review

A single concern keeps each review focused. One review's refactor can remove a whole class of another review's findings. For example, a performance or cleanliness refactor can eliminate a class of bugs.

## Workflow

1. Infer the _latest_ agreed plan/specification based on the conversation history.
2. Invoke `Skill(reviews:spec-compliance-review)`, `Skill(reviews:code-quality-review)`, and `Skill(reviews:performance-review)` in parallel. Pass each review these inputs:
   - The diff as the review scope: the `git diff` invocation for the changes against the branch that the current branch forked from. Find that branch from the common ancestor.
   - Each agreed requirement verbatim for the spec compliance review. For the other reviews, the feature's purpose and the constraints that bear on their concern, such as expected data sizes.
   - The concerns that the other reviews own, paraphrased for this feature. Add any part of the changes that the conversation excluded from review.
3. Merge duplicate findings across reviews. Drop findings that another review's fix removes, such as bugs that a proposed refactor eliminates. Resolve conflicting recommendations, such as a cache that one review proposes and another rejects as an extra layer.
4. Present every review's easy fixes and open decisions together. Wait for the user once per round, not once per review.
   - [Follow the review summary template.](./assets/review-summary-template.md)
5. Apply the merged easy fixes and accepted decisions once for all reviews before the next round.

The "Concrete Recommendations" section is particularly critical. It determines the possible next phase of the work.
