# Explicit Capabilities

Register every table capability in `tableFeatures()`. Include the feature, its required row-model factory, and only the named built-in functions that columns reference. This keeps the table contract explicit and preserves tree shaking.

```tsx
import {
	columnFilteringFeature,
	createColumnHelper,
	createFilteredRowModel,
	createSortedRowModel,
	filterFn_includesString,
	rowSortingFeature,
	sortFn_alphanumeric,
	tableFeatures,
	useTable,
} from '@tanstack/react-table';

interface Person {
	name: string;
}

// GOOD: features, row models, and named functions form one explicit capability contract.
const features = tableFeatures({
	columnFilteringFeature,
	filteredRowModel: createFilteredRowModel(),
	filterFns: { includesString: filterFn_includesString },
	rowSortingFeature,
	sortedRowModel: createSortedRowModel(),
	sortFns: { alphanumeric: sortFn_alphanumeric },
});

const columnHelper = createColumnHelper<typeof features, Person>();
const columns = columnHelper.columns([
	columnHelper.accessor('name', {
		filterFn: 'includesString',
		sortFn: 'alphanumeric',
	}),
]);

function PeopleTable({ data }: { data: Person[] }) {
	const table = useTable({ features, columns, data });
	const { rows } = table.getRowModel();
	return <div>{rows.length}</div>;
}
```

Never register the whole `filterFns`, `sortFns`, or `aggregationFns` registry. A registry bundles every function, including the ones the table never uses; import the individual `filterFn_*`, `sortFn_*`, and `aggregationFn_*` functions instead.

Never use `stockFeatures`, including in prototypes. It hides which capabilities a table depends on and defeats tree shaking.

Use `tableOptions()` to compose reusable option fragments without erasing the schema-specific options at the call site. Use `createTableHook()` for a shared table system with fixed features, defaults, or rendering primitives. Keep state ownership and table-specific `meta` explicit in the consuming schema; do not turn either helper into a global application-table abstraction.
