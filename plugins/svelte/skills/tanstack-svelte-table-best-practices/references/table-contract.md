# Table Contract

Columns are table schema. Hoist static definitions to module scope, declare their capabilities in a feature-local metadata contract, and rebuild the schema only when its structure changes.

```typescript
import {
	createColumnHelper,
	metaHelper,
	renderComponent,
	tableFeatures,
} from '@tanstack/svelte-table';

import ActionCell from './action-cell.svelte';

export interface Item {
	id: string;
	name: string;
}

interface ItemActions {
	onEdit(id: string): void;
}

// GOOD: unrelated tables do not inherit this schema's actions.
export const features = tableFeatures({
	tableMeta: metaHelper<ItemActions>(),
});

const columnHelper = createColumnHelper<typeof features, Item>();

// BAD: changing callback capture rebuilds table schema.
function createColumns(onEdit: (id: string) => void) {
	return [
		columnHelper.display({
			cell: ({ row }) => renderComponent(ActionCell, { id: row.original.id, onEdit }),
		}),
	];
}

// GOOD: hoisted schema reads its per-instance capability from table meta.
export const columns = columnHelper.columns([
	columnHelper.accessor('name', {
		header: 'Name',
		cell: info => info.getValue(),
	}),
	columnHelper.display({
		id: 'actions',
		cell: ({ row, table }) => {
			const onEdit = table.options.meta?.onEdit;
			if (typeof onEdit === 'undefined') throw new Error('Item table requires onEdit.');

			return renderComponent(ActionCell, {
				id: row.original.id,
				onEdit,
			});
		},
	}),
]);
```

Use `columnHelper.columns([...])` for every canonical column array. It preserves value inference for accessor and nested columns.

At the instance boundary, pass a stable forwarder when cells only invoke the current callback:

```svelte
<script lang="ts">
	import { createTable } from '@tanstack/svelte-table';

	import { columns, features, type Item } from './item-table-schema';

	interface Props {
		data: Item[];
		onEdit(id: string): void;
	}

	let { data, onEdit }: Props = $props();

	// GOOD: stable identity, current callback, and no schema reconstruction.
	const forwardEdit = (id: string) => onEdit(id);
	const table = createTable({
		features,
		columns,
		get data() {
			return data;
		},
		meta: { onEdit: forwardEdit },
	});
</script>
```

Use getter-backed metadata only when consumers must observe replacement of the metadata object itself. Reserve declaration merging for a contract deliberately shared by every table in the application; do not turn metadata into application state or a global capability bag.
