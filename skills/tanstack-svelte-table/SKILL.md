---
name: tanstack-svelte-table
description: Build `TanStack Table` components in Svelte using the meta field pattern for cell callbacks. Use when creating data tables, implementing sorting, or adding inline editing.
---

# TanStack Svelte Table

Data table patterns with `TanStack Table` for Svelte 5, using the `meta` field for passing behavior to cells.

## Documentation

- If available, use Context7 (Library ID: `/tanstack/table`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `TanStack/table`) for implementation details.

## Core Pattern: Hoisted Columns with Meta

```typescript
// 1. Extend TableMeta for type safety
import type { RowData } from '@tanstack/svelte-table';

declare module '@tanstack/svelte-table' {
	interface TableMeta<TData extends RowData> {
		onEdit?: (id: string) => void;
		onDelete?: (id: string) => void;
	}
}

// 2. Hoist column definitions outside component
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
<!-- 3. Pass callbacks via meta -->
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

<table>
	<thead>
		{#each table.getHeaderGroups() as headerGroup}
			<tr>
				{#each headerGroup.headers as header}
					<th>
						{#if !header.isPlaceholder}
							<svelte:component
								this={flexRender(header.column.columnDef.header, header.getContext())}
							/>
						{/if}
					</th>
				{/each}
			</tr>
		{/each}
	</thead>
	<tbody>
		{#each table.getRowModel().rows as row}
			<tr>
				{#each row.getVisibleCells() as cell}
					<td>
						<svelte:component this={flexRender(cell.column.columnDef.cell, cell.getContext())} />
					</td>
				{/each}
			</tr>
		{/each}
	</tbody>
</table>
```

## Why Meta Over Closures?

Same principle as React: closures in column definitions cause unnecessary re-renders. Hoisted columns are stable; only meta changes.

```typescript
// BAD — closures capture values, columns recreated on change
const columns = [
	{
		cell: ({ row }) => ({
			component: Button,
			props: { onclick: () => onEdit(row.original.id) }, // closure over onEdit
		}),
	},
];

// GOOD — stable columns, dynamic meta
const columns = [...]; // Hoisted, never changes

const table = createSvelteTable({
	meta: { onEdit }, // Only meta changes
});
```

## Reactive Table with Runes

```svelte
<script lang="ts">
	let { data }: Props = $props();

	// Table is reactive — re-renders when data changes
	const table = createSvelteTable({
		get data() { return data; },
		columns,
		getCoreRowModel: getCoreRowModel(),
	});
</script>
```

## Key Points

- Use `createSvelteTable` (not `useReactTable` — that's React)
- Use `flexRender` for rendering cells and headers
- Hoist column definitions outside the component for stability
- Pass callbacks through `meta`, not closures
- `TableMeta` fields should be optional (global declaration affects all tables)
