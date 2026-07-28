# Reactive Data

Pass reactive data as a getter, not a value. A plain value snapshots the state at creation, so the table never updates.

```svelte
<script lang="ts">
	import { createTable } from '@tanstack/svelte-table';

	let { data }: Props = $props();

	// BAD: snapshot, table never re-renders
	const frozen = createTable({ features, data, columns });

	// GOOD: getter keeps the table reactive
	const table = createTable({
		features,
		get data() {
			return data;
		},
		columns,
	});
</script>
```
