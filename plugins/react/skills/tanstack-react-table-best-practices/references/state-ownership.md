# State Ownership

Give every controlled slice both halves of its contract. If you provide an `on[State]Change` callback, also provide the corresponding value in `state`.

```tsx
import {
	createColumnHelper,
	createPaginatedRowModel,
	rowPaginationFeature,
	tableFeatures,
	useTable,
} from '@tanstack/react-table';
import { useState } from 'react';

interface Person {
	name: string;
}

const features = tableFeatures({
	rowPaginationFeature,
	paginatedRowModel: createPaginatedRowModel(),
});
const columnHelper = createColumnHelper<typeof features, Person>();
const columns = columnHelper.columns([columnHelper.accessor('name', { header: 'Name' })]);

function PeopleTable({ data }: { data: Person[] }) {
	const [pagination, setPagination] = useState({ pageIndex: 0, pageSize: 20 });

	// BAD: the table reports changes but never reads the controlled value back.
	const halfControlled = useTable({ features, columns, data, onPaginationChange: setPagination });

	// GOOD: the controlled value and its change callback travel together.
	const table = useTable({
		features,
		columns,
		data,
		state: { pagination },
		onPaginationChange: setPagination,
	});

	return (
		<table.Subscribe source={table.atoms.pagination} selector={state => state.pageIndex}>
			{pageIndex => <span>Page {pageIndex + 1}</span>}
		</table.Subscribe>
	);
}
```

A slice owned through the `atoms` option is the exception. With the external atom owning both the value and its updates, the slice needs no `on[State]Change` callback. External atoms take precedence over external `state`; give each slice exactly one owner.

Read state at the narrowest boundary. `table.atoms.<slice>.get()` reads one slice, but in React it is not a subscription; use it in event handlers and other non-render code. For rendered state, use the `useTable` selector, `table.Subscribe`, or the standalone `Subscribe` with an atom `source`. Read the full `table.store` snapshot only for debug output or a computation that needs every slice.
