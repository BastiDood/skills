# Reactive Result Consumption

Svelte query and mutation results are `Proxy` objects whose properties resolve dynamically. Consumers must not destructure them, including results returned by feature abstractions. Retain the result object and read its properties directly in reactive contexts. Call methods through the object, too: `query.refetch`, `mutation.mutate`, and `mutation.mutateAsync`.

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
