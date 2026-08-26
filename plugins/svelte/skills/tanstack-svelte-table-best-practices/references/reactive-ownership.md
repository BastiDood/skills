# Reactive Ownership

Pass changing options through getters and read the smallest state slice a component needs. Pair every controlled slice with its matching change callback.

```svelte
<script lang="ts">
	import {
		createColumnHelper,
		createPaginatedRowModel,
		createTable,
		createTableState,
		rowPaginationFeature,
		tableFeatures,
		type PaginationState,
	} from '@tanstack/svelte-table';

	interface Item {
		id: string;
	}

	interface Props {
		data: Item[];
	}

	let { data }: Props = $props();

	const features = tableFeatures({
		rowPaginationFeature,
		paginatedRowModel: createPaginatedRowModel(),
	});
	const columnHelper = createColumnHelper<typeof features, Item>();
	const columns = columnHelper.columns([columnHelper.accessor('id', { header: 'ID' })]);

	// BAD: the plain value snapshots the initial rows.
	const frozen = createTable({ features, data, columns });

	const [pagination, setPagination] = createTableState<PaginationState>({
		pageIndex: 0,
		pageSize: 20,
	});

	// GOOD: getters preserve reactive ownership; the callback completes controlled state.
	const table = createTable({
		features,
		columns,
		get data() {
			return data;
		},
		state: {
			get pagination() {
				return pagination();
			},
		},
		onPaginationChange: setPagination,
	});

	// GOOD: this component depends only on the pagination atom.
	const currentPagination = $derived(table.atoms.pagination.get());
</script>

<span>Page {currentPagination.pageIndex + 1}</span>
```

Use `table.store.get()` only when the component intentionally depends on all table state. `createTableState` accepts updater functions and replacement values. Use external atoms through `options.atoms` when ownership belongs outside the component; otherwise keep the slice in the component.
