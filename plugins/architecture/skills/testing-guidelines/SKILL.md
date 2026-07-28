---
name: testing-guidelines
description: Opinionated language-agnostic test-admission and sans-I/O unit-testing rules. Use when proposing, writing, reviewing, or deleting tests; deciding whether behavior is project-owned; evaluating wrapper, schema, ORM, framework, mock, or third-party-library tests; or separating pure business policy from wiring and I/O.
---

# Testing Guidelines

Read as many linked references as are relevant to the current task before writing, approving, or retaining tests.

## References

- Before adding coverage, apply [test-admission criteria](./references/test-admission.md).
- For library behavior, trust [third-party dependencies](./references/third-party-dependencies.md).
- For forwarding adapters, exclude [transparent wrappers](./references/transparent-wrappers.md).
- For pure decisions, prefer [sans-I/O tests](./references/sans-io-and-wiring-tests.md).
- During cleanup, apply the [existing-test review](./references/existing-test-review.md).
