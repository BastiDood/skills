---
name: better-all-best-practices
description: Opinionated `better-all` conventions for dependency-declared async task graphs, inferred task results, cancellation propagation, and failure semantics. Use when replacing serial `await` chains or manual `Promise.all` stages, parallelizing work with result dependencies, or writing and reviewing `all` and `allSettled` task definitions.
---

# `better-all` Best Practices

Read as many linked references as are relevant to the current task before writing or reviewing `better-all` code.

## Library Sources

- GitHub repository ID: `shuding/better-all`
- Context7 library ID: `/shuding/better-all`
- DeepWiki repository ID: `shuding/better-all`

Use Context7 for current documentation and DeepWiki for implementation details.

## References

- For sibling task results, use [task method syntax](./references/task-method-syntax.md).
- For child-operation cancellation, use [sibling cancellation](./references/sibling-cancellation.md).
- For graph failure semantics, use [library exports](./references/library-exports.md).
- For async task dependencies, use [dependency scheduling](./references/dependency-scheduling.md).
- For task-result inference, use [inferred task results](./references/inferred-task-results.md).
