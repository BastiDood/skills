# Interaction Semantics

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
