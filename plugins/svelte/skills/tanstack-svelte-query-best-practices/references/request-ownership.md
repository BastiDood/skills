# Request Ownership

Load reactive server data through TanStack Query. The query layer owns cache identity, pending state, retries, cancellation, and request deduplication.

Do not duplicate the same server-data request in a mount effect or event handler. Change the state represented in the query key, invalidate the affected key, or invoke the query API's explicit refetch operation.

```svelte
<script lang="ts">
	let filter = $state('active');
	const query = createItemsQuery(() => filter);

	// BAD: duplicates the query-owned request
	function handleFilterChange(next: string) {
		filter = next;
		fetchItems(next);
	}

	// GOOD: the key-driving state change lets the query own the request
	function handleFilterChange(next: string) {
		filter = next;
	}
</script>
```
