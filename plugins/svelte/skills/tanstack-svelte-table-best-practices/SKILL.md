---
name: tanstack-svelte-table-best-practices
description: TanStack Table v9 conventions for static Svelte table schemas and reactive instance data. Use when creating or reviewing Svelte tables, columns, metadata, or rendering.
---

# TanStack Svelte Table Best Practices

Treat a table as a static schema paired with reactive instance data and feature-local behavior; this keeps rendering, callbacks, and data updates predictable.

## Library Sources

- GitHub repository ID: `TanStack/table`
- Context7 library ID: `/tanstack/table`
- DeepWiki repository ID: `TanStack/table`

Use Context7 for current documentation and DeepWiki for implementation details.

## Effective Strategies for TanStack Svelte Table

Read the references that apply to the current task before writing or reviewing TanStack Svelte Table code.

1. Treat columns as static schema and table behavior as instance-local capability.
   - [Keep the table contract static and its capabilities feature-local](./references/table-contract.md). Pass per-instance callbacks through table meta, and reserve declaration merging for an intentional application-wide contract.
2. Provide changing rows reactively and render definitions through their table-owned context.
   - [Preserve reactive ownership](./references/reactive-ownership.md) with getter-backed options, narrow atom reads, and matching change callbacks.
   - [Render definitions, components, and snippets through adapter primitives](./references/cell-rendering.md), since raw definitions lack their table-owned context.
3. Register only the features and implementation functions that the table uses.
   - [Compose explicit, tree-shakeable capabilities](./references/explicit-capabilities.md), including individual filter, sort, and aggregation functions instead of whole registries.
4. Keep reactive ownership and table-object calls explicit.
   - [Preserve instance ownership by calling methods through their receiver](./references/instance-ownership.md); rows, cells, columns, and headers use prototype-bound methods.
5. Use the adapter helpers that preserve Svelte rendering and reusable composition.
   - [Preserve selection and logical pinning semantics](./references/interaction-semantics.md) when those capabilities are enabled.
