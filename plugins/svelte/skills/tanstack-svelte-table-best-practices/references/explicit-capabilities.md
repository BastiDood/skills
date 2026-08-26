# Explicit Capabilities

Register every non-core capability in `tableFeatures()`. This makes behavior visible in the schema and lets the bundler remove unused features.

```typescript
import {
	createPaginatedRowModel,
	createSortedRowModel,
	rowPaginationFeature,
	rowSortingFeature,
	sortFn_alphanumeric,
	tableFeatures,
} from '@tanstack/svelte-table';

// GOOD: features, row models, and named functions form one explicit capability contract.
export const features = tableFeatures({
	rowPaginationFeature,
	rowSortingFeature,
	paginatedRowModel: createPaginatedRowModel(),
	sortedRowModel: createSortedRowModel(),
	sortFns: { alphanumeric: sortFn_alphanumeric },
});
```

Register a row model only when its feature needs it. Import and register each `filterFn_*`, `sortFn_*`, or `aggregationFn_*` used by column definitions. Do not register the full function registries or use `stockFeatures` in new production code.

Use `tableOptions()` to compose reusable option fragments without hiding each table's data, columns, and state ownership. Use `createTableHook()` only when an application owns a stable shared feature set or component registry. Keep schema-specific columns, feature-local metadata, and controlled state at the call site; do not create a generic wrapper that silently adds behavior or accepts an untyped option bag.
