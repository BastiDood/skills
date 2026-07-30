- Insert a concise executive summary of the pull request in 1-2 short paragraphs.
- If applicable: mention any issue/ticket references in natural prose.
  - Action keywords such as "fixes", "resolves", and "closes" must be woven into description seamlessly.
  - Avoid lazy and abrupt insertions just for the sake of mentioning the action keywords as an afterthought.

<details>

<summary>

### Summarize the larger fix in one short blurb.

</summary>

- Group related implementation decisions under concise headings that describe their shared goal or motif.
- Explain the important implications, trade-offs, and limitations.
- Omit routine details and file-by-file change lists. Use abbreviated paths or low-fidelity pseudocode only when they clarify the design.

</details>

<!-- Ignore this section if mostly routine changes. -->

## Breaking Changes

Document the breaking changes introduced (if any), and what has been done to mitigate breakage downstream.

<!-- Ignore this section if not applicable. -->

## Test Cases

Communicate to the code reviewer that the following test cases have been considered and tested by the author. The author will be able to use this as their own personal checklist before publication.

Exclude mechanical steps that are already run in CI (e.g., running linters, formatters, unit tests, etc.) from the checklist. Only list down the critical code paths to check during manual validation. Leave the boxes unchecked.

- [ ] Test Case 1
- [ ] Test Case 2
  - [ ] Conditional Test Case 2.a
  - [ ] Conditional Test Case 2.b
  - [ ] Conditional Test Case 2.c
- [ ] Test Case 3
