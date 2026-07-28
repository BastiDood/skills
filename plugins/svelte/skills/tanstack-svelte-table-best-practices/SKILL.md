---
name: tanstack-svelte-table-best-practices
description: TanStack Table v9 beta conventions for the Svelte adapter, covering static column schema, feature-local metadata, v9 rendering, and reactive data. Use when building or reviewing Svelte data tables, defining columns, rendering cells, or wiring row-level callbacks.
---

# TanStack Svelte Table Best Practices

Read as many linked references as are relevant to the current task before writing or reviewing TanStack Svelte Table code.

## Library Sources

- GitHub repository ID: `TanStack/table`
- Context7 library ID: `/tanstack/table`
- DeepWiki repository ID: `TanStack/table`

Use Context7 for current documentation and DeepWiki for implementation details.

## Version Target

These references target TanStack Table v9 beta with Svelte 5. Use `createTable`, `FlexRender`, feature-local meta, and getter-backed reactive data. Do not mix v8 APIs such as `createSvelteTable`, global `TableMeta` augmentation, or v8 `createColumnHelper<TData>()` signatures into these examples.

## References

- For static table schema, use [hoisted columns](./references/hoisted-columns.md).
- For per-instance callbacks, use [table meta](./references/table-meta.md).
- For feature-local meta contracts, use [table meta types](./references/table-meta-types.md).
- For v9 header and cell rendering, use [cell rendering](./references/cell-rendering.md).
- For changing table data, use [reactive data](./references/reactive-data.md).
