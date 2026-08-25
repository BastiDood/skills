---
name: tanstack-react-table-best-practices
description: TanStack Table v9 conventions for stable React table schemas and instance behavior. Use when creating or reviewing React tables, columns, metadata, or rendering.
---

# TanStack React Table Best Practices

Treat a table as stable schema plus instance-specific behavior: keep columns static, place callbacks in local metadata, and render through the table primitives for reusable, type-safe tables.

## Library Sources

- GitHub repository ID: `TanStack/table`
- Context7 library ID: `/tanstack/table`
- DeepWiki repository ID: `TanStack/table`

Use Context7 for current documentation and DeepWiki for implementation details.

## Effective Strategies for TanStack React Table

Read the references that apply to the current task before writing or reviewing TanStack Table code.

1. Treat columns as static schema and table behavior as instance-local capability.
   - [Hoist columns and pass per-instance capabilities through meta](./references/hoisted-columns-and-meta.md), avoiding structural recomputation from callback capture.
   - [Declare each schema's local capability contract with typed table meta](./references/table-meta-types.md), not v8-style global metadata.
2. Render definitions through table-owned primitives because they are schema rather than JSX content.
   - [Render headers, cells, and footers through table primitives](./references/cell-rendering.md).
