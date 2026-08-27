# Interaction Semantics

## Selection and Pinning

Register `rowSelectionFeature` or column pinning only when the table exposes that behavior. Preserve the table's built-in selection handler instead of reproducing its event logic; shift-range selection is included by default.

Bind a row checkbox to `row.getIsSelected()` and pass `row.getToggleSelectedHandler()` as its change handler.

```typescript
import { rowSelectionFeature, tableFeatures, type Table } from '@tanstack/svelte-table';

interface Person {
	id: string;
}

const features = tableFeatures({ rowSelectionFeature });

// BAD: "some" means at least one, so this stays true when every row is selected.
function isIndeterminateAtFullSelection(table: Table<typeof features, Person>) {
	return table.getIsSomeRowsSelected();
}

// GOOD: indeterminate represents a genuinely partial selection.
function isIndeterminate(table: Table<typeof features, Person>) {
	return table.getIsSomeRowsSelected() && !table.getIsAllRowsSelected();
}
```

Pin columns with logical sides, `column.pin('start')` and `column.pin('end')`, and use CSS logical properties so the layout works in both text directions.

## Sort Defaults

When sorting is removed, leave `sorting` empty. If the API needs a default order, derive it without changing table state.

Keep the domain types in an ordinary TypeScript module.

```typescript
export const enum SortColumn {
	Name = 'name',
	CreatedAt = 'createdAt',
}

export interface SortRule {
	id: SortColumn;
	desc: boolean;
}
```

In this component-script excerpt, `sorting` is reactive `SortRule[]` table state. Its table bindings are omitted.

```typescript
import { SortColumn, type SortRule } from './sort';

let sorting = $state<SortRule[]>([]);

const defaultSort = { id: SortColumn.CreatedAt, desc: true } satisfies SortRule;

// BAD: writing the default back prevents removing the sort.
$effect(() => {
	if (sorting.length === 0) sorting = [defaultSort];
});

// GOOD: use the default for the request, leaving sorting untouched.
const requestSort = $derived(sorting[0] ?? defaultSort);
```

Define `defaultSort` where these parameters are built. Keep column IDs typed through the UI and API; do not add casts or runtime parsing. Use TanStack's sorting cycle instead of writing your own.

## Filters and Pagination

For server-side sorting and filtering, send the sort and filter values to the server. Restart pagination when those values change.

These table-option excerpts use reactive `pageRows`, one page returned by the server. Other options are omitted.

```typescript
// BAD: local sorting and filtering can only process this page.
const localOptions = {
	get data() {
		return pageRows;
	},
	manualSorting: false,
	manualFiltering: false,
};

// GOOD: the server sorts and filters before returning the page.
const serverOptions = {
	get data() {
		return pageRows;
	},
	manualSorting: true,
	manualFiltering: true,
};
```

Use server totals for complete result counts. `pageRows.length` describes only the loaded page.
