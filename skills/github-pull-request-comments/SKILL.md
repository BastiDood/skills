---
name: github-pull-request-comments
description: Verifies, triages, and rebuts unresolved GitHub pull request comments. Use when planning how to resolve feedback on a PR.
compatibility: Requires the GitHub CLI (`gh`).
---

Fetch all unresolved comments on the current pull request. Triage them based on whether the feedback is valid, actionable, or in-scope of the intended work.

Add accepted/confirmed items to your work plan of proposed amendments. Each item must be corroborated by concrete evidence. Confirm through static analysis, runtime experimentation, or manual verification that the concern is warranted. Use "Plan Mode" when deliberating on the next implementation steps.

Otherwise, compile rejected/rebutted items into a listing in the message summary _separately_ from the proposed amendments. Each item must also be corroborated by concrete contradictions to earlier validations. Format each rebuttal as follows:

<rebuttal_format>

> Exact quote from the PR comment.

```
Concise response to the reviewer explaining why the comment is invalid, actionable, or out of scope.
```

</rebuttal_format>

Be polite, concise, and technical when delivering your rebuttal, justification, and evidence.
