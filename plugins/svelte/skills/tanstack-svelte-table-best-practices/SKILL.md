---
name: tanstack-svelte-table-best-practices
description: TanStack Table v9 beta conventions for the Svelte adapter, covering static column schema, feature-local metadata, v9 rendering, and reactive data. Use when building or reviewing Svelte data tables, defining columns, rendering cells, or wiring row-level callbacks.
---

# TanStack Svelte Table Best Practices

Treat a table as a static schema paired with reactive instance data and feature-local behavior; this keeps rendering, callbacks, and data updates predictable under the v9 Svelte API.

## Library Sources

- GitHub repository ID: `TanStack/table`
- Context7 library ID: `/tanstack/table`
- DeepWiki repository ID: `TanStack/table`

Use Context7 for current documentation and DeepWiki for implementation details.

## Version Target

These references target TanStack Table v9 beta with Svelte 5. Use `createTable`, `FlexRender`, feature-local meta, and getter-backed reactive data. Do not mix v8 APIs such as `createSvelteTable`, global `TableMeta` augmentation, or v8 `createColumnHelper<TData>()` signatures into these examples.

## References

Read as many linked references as are relevant to the current task before writing or reviewing TanStack Svelte Table code.

- Hoist [hoisted columns](./references/hoisted-columns.md) as static schema, rebuilding them only when the schema itself changes.
- Pass per-instance callbacks through [table meta](./references/table-meta.md) so hoisted columns do not capture changing component values.
- Supply changing rows as a getter with [reactive data](./references/reactive-data.md), because a plain option snapshots the initial value.
- Define [table meta types](./references/table-meta-types.md) beside their schema so unrelated tables do not inherit irrelevant capabilities.
- Render v9 definitions through [cell rendering](./references/cell-rendering.md), since a raw column definition lacks its table-owned context.
