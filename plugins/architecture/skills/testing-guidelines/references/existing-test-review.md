# Existing Test Review

Delete a test when it only:

- Repeats third-party documentation.
- Asserts schema-library mechanics.
- Verifies a one-to-one forwarding wrapper.
- Asserts mock configuration.
- Checks getters, constructors, or field assignment without behavior.
- Exercises framework plumbing without a project-owned contract.

Do not preserve a useless test for coverage. Coverage is evidence of execution, not evidence of meaningful verification.
