# Instance Ownership

Call methods on their row, cell, column, or header. These methods live on shared prototypes, so extracting one loses its receiver.

```typescript
import { tableFeatures, type Row } from '@tanstack/svelte-table';

interface Person {
	name: string;
}

const features = tableFeatures({});

// BAD: the extracted method has lost the row that supplies its data and caches.
function readNameWithoutOwner(row: Row<typeof features, Person>) {
	const { getValue } = row;
	return getValue('name');
}

// GOOD: call the shared method through the object that owns its state.
function readName(row: Row<typeof features, Person>) {
	return row.getValue('name');
}
```

Apply the same rule to event handlers: pass the result of `row.getToggleSelectedHandler()` instead of extracting the method itself.
