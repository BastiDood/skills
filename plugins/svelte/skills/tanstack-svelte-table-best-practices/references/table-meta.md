# Table Meta

Pass per-instance callbacks and context through table `meta`; do not capture changing component values in hoisted column definitions. Capturing those values forces structural column recomputation.

```typescript
// BAD: columns capture per-instance behavior when they are created.
const columns = createColumns(onEdit);

// GOOD: stable forwarders read the current callback when invoked.
const forwardEdit = (id: string) => onEdit(id);
const forwardDelete = (id: string) => onDelete(id);

const table = createTable({
	features,
	get data() {
		return data;
	},
	columns,
	meta: { onEdit: forwardEdit, onDelete: forwardDelete },
});
```

The `features` value declares the shape of `meta` with `tableMeta: metaHelper<...>()`. Do not put a reactive getter on `meta`; table options are not a reactive state slice. A stable forwarding function can read the current Svelte prop when the cell invokes it.
