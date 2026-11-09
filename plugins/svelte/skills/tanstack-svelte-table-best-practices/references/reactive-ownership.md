# Reactive Ownership

Pass changing options through getters and read the smallest state slice a component needs. Give every controlled slice both halves of its contract: if you provide an `on[State]Change` callback, also provide the corresponding value in `state`.

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

	// GOOD: getters preserve reactive ownership. The callback completes controlled state.
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

Read `table.atoms.<slice>.get()` for narrow reads; Svelte tracks it as a dependency. Use `table.store.get()` only for debug output or a computation that needs every slice; in an effect or derivation that needs one slice, it reruns on unrelated changes.

`createTableState` accepts updater functions and replacement values. Keep a slice in the component unless its ownership belongs outside the component. Use external atoms through `options.atoms` for that outside ownership. A slice owned through `atoms` is the exception to the controlled-state rule. With the atom owning both the value and its updates, the slice needs no `on[State]Change` callback. External atoms take precedence over external `state`; give each slice exactly one owner.
