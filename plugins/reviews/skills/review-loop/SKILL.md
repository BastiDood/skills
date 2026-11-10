---
name: review-loop
description: Use when reviewing prior work in a loop until no more findings are worth accepting.
---

# Review Loop

You review a body of work against explicit criteria, in rounds, until no finding is worth accepting.

## Review Brief

Infer each part of the brief from the request, the conversation, and the code. Prefer the latest version of any agreement. Ask the user only about a gap that changes what the reviewer looks for. A part that the request or a specializing skill supplies replaces the inferred one. Placeholders that supplied criteria add are part of the brief.

- **Criteria:** what the reviewer judges the code against, at the breadth that the request asks for. Name the evidence that proves a finding. "Accessibility" invites a checklist. "Keyboard reachability of the new form controls, proven by the rendered markup" directs the search. The brief can hold several criteria sets.
- **Scope:** the code under review and its boundaries. Default to the current branch's changes since its merge base.
- **Feature Context:** the feature's purpose, its callers, its exposure to untrusted input, and the constraints that bear on the criteria. The reviewer judges what is realistic from it.
- **Excluded Criteria:** the criteria that each reviewer leaves out, such as the criteria that another set owns.
- **Presentation:** the order and format of the findings. Default to the order of impact. Criteria that rank their findings define impact.

## Workflow

1. For each criteria set, fill the [review prompt](./prompts/review-scope.md) from the brief and the review log. Omit each section left empty. Send each prompt as the whole message to a new (non-forked) sub-agent. Run the reviewers in parallel.
2. Drop each finding that breaks the prompt's "Evidence" rule. Merge duplicate findings. Drop each finding that another finding's fix removes. Resolve conflicting fixes. If no finding remains, report all-clear and stop.
3. Present the findings as the "Presentation" part specifies. Propose each finding with one clear fix as an easy fix. Ask the user about each other finding as an open decision. Wait for the answers.
4. Log each finding as accepted or rejected, with the reason. The log is the next reviewer's only memory.
5. If you accepted nothing, stop. Otherwise, apply the accepted fixes and return to step 1.
