# Reactive Result Consumption

Never destructure query or mutation result objects, including results returned by feature abstractions. Read fields directly to preserve reactivity. Call methods directly, too: `query.refetch`, `mutation.mutate`, and `mutation.mutateAsync`.

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

```typescript
// BAD: extracts state and a method from the mutation result.
const { isPending, mutate } = createUpdateItemMutation();
mutate(variables);

// GOOD: retains the mutation object and calls its method directly.
const mutation = createUpdateItemMutation();
mutation.mutate(variables);
```

Do not pass the complete result object through component props for convenience. Keep query-state handling in the loader and pass resolved data to presentation.
