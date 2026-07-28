# Outer Loader

Use a loader component as the query-state boundary between a shell and resolved presentation. Handle pending, error, empty, and success states before mounting the inner component.

```svelte
<!-- BAD: unresolved state leaks into presentation. -->
<ItemsForm items={query.data} isPending={query.isPending} />

<!-- GOOD: the loader resolves every state before mounting presentation. -->
<script lang="ts">
	const query = createItemsQuery();
</script>

{#if query.isPending}
	<ItemsSkeleton />
{:else if query.isError}
	<ErrorBanner error={query.error} />
{:else if typeof query.data === 'undefined' || query.data.length === 0}
	<EmptyItems />
{:else}
	<ItemsForm items={query.data} />
{/if}
```

The loader alone owns query execution and status handling. Keep resolved presentation and owned business decisions sans-I/O when they deserve direct unit tests.

Keep mutations in a narrow form driver below the resolved boundary. A component that owns a mutation is an I/O driver and does not become unit-testable merely because it sits below the loader. Mount the loader only while conditionally visible UI is open, and mount stateful presentation only after its required data resolves.
