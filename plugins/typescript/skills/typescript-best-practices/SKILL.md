---
name: typescript-best-practices
description: Opinionated TypeScript conventions for inference-first typing, precise contracts, finite-state modelling, exhaustive decisions, and strict handling of optional or untrusted values. Use when writing or reviewing TypeScript types, state transitions, API signatures, schema-adjacent parsing, array or regular-expression access, or strict compiler errors.
---

# TypeScript Best Practices

Read as many linked references as are relevant to the current task before writing or reviewing TypeScript.

## References

- For redundant annotations, use [inference over annotation](./references/inference-over-annotation.md).
- For widened closed states, use [literal union inference](./references/literal-union-inference.md).
- For serialized domain values, use [const enums](./references/const-enums.md).
- For declaration selection, use [interfaces and types](./references/interfaces-and-types.md).
- For casts over untrusted values, avoid [unchecked assertions](./references/unchecked-assertions.md).
- For retained literal inference, use [literal const assertions](./references/literal-const-assertions.md).
- For shape conformance with inference, use [structural conformance](./references/structural-conformance.md).
- For unsafe broad types, restrict [type escape hatches](./references/escape-hatches.md).
- For invalid state combinations, use [discriminated union state](./references/discriminated-union-state.md).
- For closed-set handling, use [exhaustive decisions](./references/exhaustive-decisions.md).
- For potentially absent values, avoid [non-null assertions](./references/non-null-assertions.md).
- For omitted versus `undefined` arguments, use [optional arguments](./references/optional-arguments.md).
- For required optional values, use [optional value narrowing](./references/optional-value-narrowing.md).
- For invented fallback input, reject [fabricated defaults](./references/no-fabricated-defaults.md).
- For possibly absent array entries, use [array index guards](./references/array-index-guards.md).
- For optional parsing pieces, use [split results](./references/split-results.md).
- For required regex captures, use [regular expression capture groups](./references/regex-capture-groups.md).
