- Insert executive summary of the pull request here in 1-3 short paragraphs.
- If applicable: mention any issue/ticket references in natural prose.
  - Action keywords such as "fixes", "resolves", and "closes" must be woven into description seamlessly.
  - Avoid lazy and abrupt insertions just for the sake of mentioning the action keywords as an afterthought.

## Implementation Notes

- Group the detailed implementation notes into proper sub-sections with concise headings.
- Expound on the implications of the details noted in the preceding summary.
- Insert key architectural decisions here (if any) and their justifications.
- Strive to surface the most relevant implementation features, drawbacks, and limitations to the reviewer without overwhelming them with exact code snippets and file names.
  - Code snippets and file names are still permitted (if necessary), but only for demonstrative purposes.
  - Low-fidelity pseudocode and abbreviated file paths are preferred.

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

<!-- Remind the author that these test cases have to be validated manually before submitting the pull request. -->
