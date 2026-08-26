# Interaction Semantics

Use `row.getToggleSelectedHandler()` and `table.getToggleAllRowsSelectedHandler()` directly as event handlers. Shift-range selection is enabled by default; set `enableRowRangeSelection: false` only when range selection is not part of the product behavior.

```typescript
import { rowSelectionFeature, tableFeatures, type Table } from '@tanstack/react-table';

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

Use logical `start` and `end` pinning positions and CSS logical inset properties for layouts that support right-to-left text. Do not introduce left/right assumptions into new pinning UI.
