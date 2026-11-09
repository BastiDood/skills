---
name: spec-compliance-review
description: Use when auditing whether code behaves as the agreed plan or specification requires.
---

# Spec Compliance Review

Reviewers are stateless by design. The review log is the only memory between rounds.

## Workflow

1. Take each agreed requirement from the request. Otherwise, infer the _latest_ agreed plan/specification based on the conversation history.
2. Fill every placeholder in the [review prompt](./prompts/review-scope.md) and spawn a new (non-forked) sub-agent with the filled prompt as its whole message.
   - Fill `{{SCOPE}}` with the code that the request asks to review, such as files, a module, a feature, or a diff.
   - Fill `{{SPECIFICATION}}` with each agreed requirement verbatim.
   - Fill `{{OUT_OF_SCOPE}}` with the concerns that the request excludes. Omit the "Out of Scope" section when the request excludes none.
   - Fill `{{REVIEW_LOG}}` with the review log. Omit the "Review Log" section on the first round.
3. If the reviewer flags nothing, present the round as all-clear and stop. Otherwise, sort the findings. A finding with one clear fix is an easy fix. A finding that needs a product or engineering decision is an open decision.
4. Present the findings in the [review summary template](./assets/review-summary-template.md). Propose each easy fix. Ask the user about each open decision. Wait for the user's answers.
5. Record each finding in the review log as accepted or rejected. Give the reason. Keep the log as free-form text.
6. Apply the easy fixes and the accepted decisions before the next round. Return to step 2 with the updated log. Stop when a round returns no finding that you accept.

The "Concrete Recommendations" section is particularly critical. It determines the possible next phase of the work.
