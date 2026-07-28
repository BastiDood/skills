# Reactive Result Consumption

Read query-result properties directly from the object returned by `createQuery`. Do not destructure reactive fields into ordinary variables, which snapshots their current values and stops template updates.

```svelte
<script lang="ts">
	const query = createItemsQuery();

	// BAD: snapshots the current values
	const { data, isPending } = query;
</script>

<!-- GOOD: property reads remain reactive -->
{#if query.isPending}
	<ItemsSkeleton />
{:else if typeof query.data !== 'undefined'}
	<ItemsList items={query.data} />
{/if}
```

Do not pass the complete result object through component props for convenience. Keep query-state handling in the loader and pass resolved data to presentation.
