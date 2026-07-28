---
name: valibot-best-practices
description: Opinionated Valibot conventions for wire-schema ownership, nullability, tagged variants, transformations, and explicit validation-failure behavior. Use when defining or reviewing Valibot schemas, validating forms or HTTP payloads, parsing JSON or persisted data, or deciding whether invalid input aborts or becomes an expected branch.
---

# Valibot Best Practices

Read as many linked references as are relevant to the current task before defining or reviewing Valibot schemas and parsing code.

## Library Sources

- GitHub repository ID: `open-circle/valibot`
- Context7 library ID: `/open-circle/valibot`
- DeepWiki repository ID: `open-circle/valibot`

Use Context7 for current documentation and DeepWiki for implementation details.

## References

- For Valibot import clarity, use [namespace imports](./references/namespace-imports.md).
- For schema/type duplication, use [schema ownership](./references/schema-ownership.md).
- For missing or `null` wire values, use [wire nullability](./references/wire-nullability.md).
- For tagged object alternatives, use [discriminated variants](./references/discriminated-variants.md).
- For typed data ingress, use [serialized trust boundaries](./references/serialized-trust-boundaries.md).
- For malformed-input handling, use [parse failure contracts](./references/parse-failure-contracts.md).
- For failed-validation branches, use [validation failure preservation](./references/validation-failure-preservation.md).
- For omitted wire defaults, use [schema defaults](./references/schema-defaults.md).
- For wire-value normalization, use [schema transformations](./references/schema-transformations.md).
