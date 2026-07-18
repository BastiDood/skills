---
name: tanstack-react-table-best-practices
description: Opinionated TanStack Table conventions for React using hoisted columns and the meta field pattern. Use when building data tables or wiring row-level callbacks.
---

# TanStack React Table Best Practices

Opinionated conventions for TanStack Table in React. API documentation lives elsewhere (see the footer); every section here is a rule.

## Hoisted Columns with Meta

Hoist column definitions to module scope and pass behavior through `meta`. Columns stay referentially stable; only the meta object varies per table instance.

```tsx
// 1. Extend TableMeta for type safety
declare module '@tanstack/react-table' {
	interface TableMeta<TData extends RowData> {
		onEdit?: (id: string) => void;
		onDelete?: (id: string) => void;
	}
}

// 2. Hoist column definitions outside the component
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

## Meta over Closures

Closures in column definitions force the column array to be rebuilt whenever the captured value changes, invalidating the whole table:

```typescript
// BAD - new column array every render
const columns = useMemo(() => [
	{
		cell: ({ row }) => (
			<Button onClick={() => onEdit(row.original.id)}>Edit</Button>
		),
	},
], [onEdit]); // Invalidates when onEdit changes

// GOOD - stable columns, dynamic meta
const columns = [...]; // Hoisted, never changes

const table = useReactTable({
	meta: { onEdit }, // Only meta changes
});
```

## Optional Fields in the Global `TableMeta`

The `declare module` augmentation of `TableMeta` is global — it affects EVERY table in the codebase. Keep all fields optional so tables that don't need a callback aren't forced to provide it.

```typescript
import type { RowData } from '@tanstack/react-table';

declare module '@tanstack/react-table' {
	interface TableMeta<TData extends RowData> {
		onEdit?: (id: string) => void;
		onDelete?: (id: string) => void;
		onSelect?: (id: string, selected: boolean) => void;
		isLoading?: boolean;
	}
}

// Tables that don't need onEdit aren't forced to provide it
const simpleTable = useReactTable({ data, columns: simpleColumns });

// Tables that need callbacks provide them
const editableTable = useReactTable({ data, columns: editableColumns, meta: { onEdit } });
```

## Documentation

- If available, use Context7 (Library ID: `/tanstack/table`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `TanStack/table`) for implementation details.
