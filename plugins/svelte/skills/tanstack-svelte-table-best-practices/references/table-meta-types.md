# Table Meta Types

Define table meta through a feature-local contract. Do not augment TanStack Table's global types: unrelated tables must not inherit one table's capabilities.

```typescript
import { metaHelper, tableFeatures } from '@tanstack/svelte-table';

export interface ItemActions {
	onDelete(id: string): void;
	onEdit(id: string): void;
}

// BAD: one broad feature set gives unrelated tables irrelevant capabilities.
interface SharedTableActions extends ItemActions {
	onArchive(id: string): void;
}

export const sharedFeatures = tableFeatures({
	tableMeta: metaHelper<SharedTableActions>(),
});

// GOOD: this feature set owns the capabilities used by its columns.
export const features = tableFeatures({
	tableMeta: metaHelper<ItemActions>(),
});
```

Keep the feature set beside its column schema. Columns infer the local meta contract through `typeof features`; unrelated tables define their own feature set and expose no irrelevant actions.
