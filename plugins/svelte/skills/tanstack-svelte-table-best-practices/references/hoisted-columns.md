# Hoisted Columns

Columns are table schema. Hoist static column definitions to module scope and rebuild them only when the schema itself changes.

```typescript
import { createColumnHelper, renderComponent } from '@tanstack/svelte-table';

import ActionCell from './action-cell.svelte';
import { features } from './table-features';

// BAD: changing callback capture rebuilds table schema.
function createColumns(onEdit: (id: string) => void) {
	return [
		columnHelper.display({
			cell: ({ row }) => renderComponent(ActionCell, { id: row.original.id, onEdit }),
		}),
	];
}

// GOOD: hoisted schema reads its per-instance capability from table meta.
const columnHelper = createColumnHelper<typeof features, Item>();

export const columns = [
	columnHelper.accessor('name', {
		header: 'Name',
		cell: info => info.getValue(),
	}),
	columnHelper.display({
		id: 'actions',
		cell: ({ row, table }) =>
			renderComponent(ActionCell, {
				id: row.original.id,
				onEdit: table.options.meta.onEdit,
			}),
	}),
];
```
