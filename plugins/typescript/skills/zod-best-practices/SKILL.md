---
name: zod-best-practices
description: Opinionated Zod v4 conventions for schema ownership, format validation, nullability, tagged unions, object-key contracts, transformations, and explicit validation-failure behavior. Use when defining or reviewing Zod schemas, validating forms or HTTP payloads, parsing JSON or persisted data, rendering validation errors, or deciding whether invalid input aborts or becomes an expected branch.
---

# Zod Best Practices

Read as many linked references as are relevant to the current task before defining or reviewing Zod schemas and parsing code.

## Library Sources

- GitHub repository ID: `colinhacks/zod`
- Context7 library ID: `/colinhacks/zod`
- DeepWiki repository ID: `colinhacks/zod`

Use Context7 for current documentation and DeepWiki for implementation details.

## References

- For Zod import clarity, use [Zod imports](./references/zod-imports.md).
- For schema or format duplication, use [schema ownership and formats](./references/schema-ownership-and-formats.md).
- For schema input/output differences, use [schema input and output](./references/schema-input-output.md).
- For standard string formats, use [top-level format validators](./references/top-level-formats.md).
- For missing or `null` wire values, use [wire nullability](./references/wire-nullability.md).
- For omitted wire defaults, use [schema defaults](./references/schema-defaults.md).
- For tagged object alternatives, use [discriminated unions](./references/discriminated-unions.md).
- For unknown object keys, use [object strictness](./references/object-strictness.md).
- For typed data ingress, use [serialized trust boundaries](./references/serialized-trust-boundaries.md).
- For malformed-input handling, use [parse failure contracts](./references/parse-failure-contracts.md).
- For validation error presentation, use [validation error rendering](./references/validation-error-rendering.md).
- For single-value rules, use [predicate validation](./references/predicate-validation.md).
- For multi-field rules, use [cross-field validation](./references/cross-field-validation.md).
