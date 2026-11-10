---
name: review-loop
description: Use when reviewing prior work in a loop until no more findings are worth accepting.
---

# Review Loop

You have been tasked to review the recent piece of work.

## Review Brief

Infer each part of the brief from the request, the conversation, and the code. Prefer the latest version of any agreement. Ask the user only about a gap that changes what the reviewer looks for. A part that the request or a specializing skill supplies replaces the inferred one. Placeholders that supplied criteria add are part of the brief.

- **Criteria:** what the reviewer judges the code against, at the breadth that the request asks for. Name the evidence that proves a finding. "Accessibility" invites a checklist. "Keyboard reachability of the new form controls, proven by the rendered markup" directs the search.
- **Scope:** the code under review and its boundaries. Default to the current branch's changes since its merge base.
- **Feature context:** the feature's purpose, its callers, its exposure to untrusted input, and the constraints that bear on the criteria. The reviewer judges what is realistic from it.
- **Excluded criteria:** the criteria that the request leaves out.
- **Presentation:** the order and format of the findings. Default to the order of impact.

## Workflow

1. Fill the [review prompt](./prompts/review-scope.md) from the brief and the review log. Omit each section left empty. Send it as the whole message to a new (non-forked) sub-agent.
2. Drop each finding that breaks the prompt's "Evidence" rule. If none remain, report all-clear and stop.
3. Present the findings as the "Presentation" part specifies. Propose each finding with one clear fix as an easy fix. Ask the user about each other finding as an open decision. Wait for the answers.
4. Log each finding as accepted or rejected, with the reason. The log is the next reviewer's only memory.
5. If you accepted nothing, stop. Otherwise, apply the accepted fixes and return to step 1.
