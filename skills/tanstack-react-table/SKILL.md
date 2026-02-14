---
name: tanstack-react-table
description: Build TanStack Table components in React using the meta field pattern for cell callbacks. Use when creating data tables, implementing sorting, or adding inline editing.
---

# TanStack React Table

Data table patterns with TanStack Table for React, using the `meta` field for passing behavior to cells.

## Documentation

- If available, use `context7` (Library ID: `/tanstack/table`) to fetch the latest documentation.
- If available, use `deepwiki` (GitHub Repository: `TanStack/table`) for implementation details.

## Core Pattern: Hoisted Columns with Meta

```typescript
// 1. Extend TableMeta for type safety
declare module '@tanstack/react-table' {
	interface TableMeta<TData extends RowData> {
		onEdit?: (id: string) => void;
		onDelete?: (id: string) => void;
	}
}

// 2. Hoist column definitions outside component
const columnHelper = createColumnHelper<Job>();

const columns = [
	columnHelper.accessor('name', {
		header: 'Name',
		cell: info => info.getValue(),
	}),
	columnHelper.display({
		id: 'actions',
		cell: ({ row, table }) => (
			<Button onClick={() => table.options.meta?.onEdit?.(row.original.id)}>Edit</Button>
		),
	}),
];

// 3. Pass callbacks via meta
function DataTable({ data, onEdit, onDelete }: Props) {
	const table = useReactTable({
		data,
		columns,
		getCoreRowModel: getCoreRowModel(),
		meta: { onEdit, onDelete },
	});

	return <Table>...</Table>;
}
```

## Why Meta Over Closures?

Closures in column definitions cause re-renders:

```typescript
// BAD — new column array every render
const columns = useMemo(() => [
	{
		cell: ({ row }) => (
			<Button onClick={() => onEdit(row.original.id)}>Edit</Button>
		),
	},
], [onEdit]); // Invalidates when onEdit changes

// GOOD — stable columns, dynamic meta
const columns = [...]; // Hoisted, never changes

const table = useReactTable({
	meta: { onEdit }, // Only meta changes
});
```

## Type-Safe Meta Extension

```typescript
import { RowData } from '@tanstack/react-table';

declare module '@tanstack/react-table' {
	interface TableMeta<TData extends RowData> {
		// Optional to avoid affecting other tables
		onEdit?: (id: string) => void;
		onDelete?: (id: string) => void;
		onSelect?: (id: string, selected: boolean) => void;
		isLoading?: boolean;
	}
}
```

## Accessing Meta in Cells and Headers

```typescript
// In cells
columnHelper.display({
	id: 'actions',
	cell: ({ row, table }) => {
		const meta = table.options.meta;
		return (
			<div>
				<Button onClick={() => meta?.onEdit?.(row.original.id)}>Edit</Button>
				<Button onClick={() => meta?.onDelete?.(row.original.id)} disabled={meta?.isLoading}>
					Delete
				</Button>
			</div>
		);
	},
});

// In headers
columnHelper.accessor('name', {
	header: ({ table }) => {
		const meta = table.options.meta;
		return (
			<div className="flex items-center">
				Name
				{meta?.isLoading && <Spinner className="ml-2" />}
			</div>
		);
	},
});
```

## Why Optional Fields?

`TableMeta` is global — it affects ALL tables. Making fields optional means tables that don't need specific callbacks aren't forced to provide them:

```typescript
// Tables that don't need onEdit aren't forced to provide it
const simpleTable = useReactTable({ data, columns: simpleColumns });

// Tables that need callbacks provide them
const editableTable = useReactTable({ data, columns: editableColumns, meta: { onEdit } });
```
