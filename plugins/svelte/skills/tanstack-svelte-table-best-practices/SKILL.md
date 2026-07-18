---
name: tanstack-svelte-table-best-practices
description: Opinionated TanStack Table conventions for Svelte using hoisted columns, the meta field pattern, and reactive data getters. Use when building data tables or wiring row-level callbacks.
---

# TanStack Svelte Table Best Practices

Opinionated conventions for TanStack Table in Svelte 5. API documentation lives elsewhere (see the footer); every section here is a rule.

## Hoisted Columns with Meta

Hoist column definitions to module scope and pass behavior through `meta`. Columns stay referentially stable; only the meta object varies per table instance.

```typescript
// 1. Extend TableMeta for type safety
import type { RowData } from '@tanstack/svelte-table';

declare module '@tanstack/svelte-table' {
	interface TableMeta<TData extends RowData> {
		onEdit?: (id: string) => void;
		onDelete?: (id: string) => void;
	}
}

// 2. Hoist column definitions outside the component
const columnHelper = createColumnHelper<Item>();

const columns = [
	columnHelper.accessor('name', {
		header: 'Name',
		cell: info => info.getValue(),
	}),
	columnHelper.display({
		id: 'actions',
		cell: ({ row, table }) => {
			const meta = table.options.meta;
			return {
				component: ActionCell,
				props: { id: row.original.id, onEdit: meta?.onEdit, onDelete: meta?.onDelete },
			};
		},
	}),
];
```

```svelte
<!-- 3. Pass callbacks via meta; render with flexRender -->
<script lang="ts">
	import { createSvelteTable, getCoreRowModel, flexRender } from '@tanstack/svelte-table';

	const { data, onEdit, onDelete }: Props = $props();

	const table = createSvelteTable({
		data,
		columns,
		getCoreRowModel: getCoreRowModel(),
		meta: { onEdit, onDelete },
	});
</script>

<tbody>
	{#each table.getRowModel().rows as row}
		<tr>
			{#each row.getVisibleCells() as cell}
				{@const Cell = flexRender(cell.column.columnDef.cell, cell.getContext())}
				<td><Cell /></td>
			{/each}
		</tr>
	{/each}
</tbody>
```

Headers render the same way via `table.getHeaderGroups()`. Render the component directly from an `{@const}` — `<svelte:component this={...}>` is deprecated in Svelte 5.

## Meta over Closures

Same principle as React: closures in column definitions force the column array to be rebuilt whenever the captured value changes. Hoisted columns are stable; only meta changes.

```typescript
// BAD - closures capture values, columns recreated on change
const columns = [
	{
		cell: ({ row }) => ({
			component: Button,
			props: { onclick: () => onEdit(row.original.id) }, // closure over onEdit
		}),
	},
];

// GOOD - stable columns, dynamic meta
const columns = [...]; // Hoisted, never changes

const table = createSvelteTable({
	meta: { onEdit }, // Only meta changes
});
```

`TableMeta` fields stay optional — the `declare module` augmentation is global, so required fields would burden every table in the codebase.

## Reactive Data via Getters

Pass reactive data as a getter, not a value. A plain value snapshots the state at creation, so the table never updates.

```svelte
<script lang="ts">
	let { data }: Props = $props();

	// BAD - snapshot, table never re-renders
	const frozen = createSvelteTable({ data, columns, getCoreRowModel: getCoreRowModel() });

	// GOOD - getter keeps the table reactive
	const table = createSvelteTable({
		get data() { return data; },
		columns,
		getCoreRowModel: getCoreRowModel(),
	});
</script>
```

## Documentation

- If available, use Context7 (Library ID: `/tanstack/table`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `TanStack/table`) for implementation details.
