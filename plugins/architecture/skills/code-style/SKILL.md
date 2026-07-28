---
name: code-style
description: Opinionated language-agnostic design rules for implementation, refactoring, and code review. Use when shaping conditional control flow, finite states, invariants, error boundaries, resource lifetimes, abstractions, public APIs, validation boundaries, pagination, missing values, or the separation of sans-I/O decisions from imperative drivers.
---

# Code Style

Read as many linked references as are relevant to the current task before writing or reviewing code.

Treat each directive as a language-agnostic semantic requirement. Adapt its TypeScript example to the target language's native syntax, type system, resource-management mechanisms, and compiler checks.

## Directives

- For negated guards, prefer [affirmative conditions](./references/affirmative-conditions.md).
- For nested exceptional paths, use [guard clauses](./references/guard-clauses.md).
- For hidden decisions, prefer [explicit behavior](./references/explicit-over-implicit.md).
- For concise value selection, use [conditional expressions](./references/conditional-expressions.md).
- For contradictory fields, [enforce invariants](./references/enforce-invariants.md).
- For violated internal invariants, [fail impossible states](./references/fail-impossible-states.md).
- For finite states, require [exhaustive decisions](./references/exhaustive-decisions.md).
- For expected failures, keep [error boundaries narrow](./references/narrow-error-boundaries.md).
- For helpers and wrappers, demand [simple abstractions](./references/simple-abstractions.md).
- For closeable resources, [bind ownership to lifetime](./references/resources-own-lifetimes.md).
- For dependency results, [preserve caller-relevant information](./references/preserve-information.md).
- For paginated sources, expose [lazy pagination](./references/lazy-pagination.md).
- For external payloads, [validate at boundaries](./references/validate-at-boundaries.md).
- For missing required values, reject [fabricated defaults](./references/no-fabricated-defaults.md).
- For exports, keep [public surfaces narrow](./references/narrow-public-surfaces.md).
- For I/O-heavy decisions, separate the [pure core](./references/pure-core-imperative-shell.md) for sans-I/O.
