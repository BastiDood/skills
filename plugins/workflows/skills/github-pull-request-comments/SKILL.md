---
name: github-pull-request-comments
description: Verify and triage unresolved GitHub PR feedback into amendment plans or evidence-backed rebuttals. Use when planning responses to review comments.
compatibility: Requires the GitHub CLI (`gh`).
---

# GitHub Pull Request Comments

Evaluate unresolved pull request feedback against concrete evidence, convert confirmed in-scope issues into a focused amendment plan, and provide precise, respectful rebuttals for feedback that does not warrant changes.

Add accepted/confirmed items to your work plan of proposed amendments. Each item must be corroborated by concrete evidence. Confirm through static analysis, runtime experimentation, or manual verification that the concern is warranted. Use "Plan Mode" when deliberating on the next implementation steps.

Otherwise, compile rejected/rebutted items (and their hyperlinks) into a listing in the message summary _separately_ from the proposed amendments. Each item must also be corroborated by concrete contradictions to earlier validations. Format each rebuttal as follows:

<rebuttal_format>

> [Exact quote from the PR comment.](https://github.com/{org}/{repo}/pull/{pr_number}#discussion_{comment_id})

```markdown
Concise response to the reviewer explaining why the comment is invalid, unactionable, or out of scope.
```

</rebuttal_format>

Be polite, concise, and technical when delivering your rebuttal, justification, and evidence.
