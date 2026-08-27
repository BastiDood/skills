# Request Ownership

Load reactive server data through TanStack Query. A feature query abstraction owns input normalization and derivation, cache identity, options, request construction, and the `createQuery` call. TanStack Query owns pending state, retries, cancellation, and request deduplication.

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

```typescript
// items-query.svelte.ts
import { createQuery } from '@tanstack/svelte-query';

function normalizeItemFilter(filter: string) {
	return filter.trim().toLowerCase();
}

async function fetchItems(filter: string, signal: AbortSignal) {
	const response = await fetch('/api/items?filter=' + encodeURIComponent(filter), { signal });
	if (!response.ok) throw new Error('Items request failed');
	return await response.text();
}

export function createItemsQuery(getFilter: () => string) {
	const filter = $derived(normalizeItemFilter(getFilter()));

	return createQuery(() => ({
		queryKey: ['items', 'list', filter] as const,
		queryFn: async ({ queryKey: [, , keyFilter], signal }) => {
			return await fetchItems(keyFilter, signal);
		},
	}));
}
```

Keep reusable reactive query code in a `.svelte.ts` module. Pass getters for reactive inputs so the feature can derive current values without a construction-time snapshot. Cache non-constant derivation with `$derived` inside that feature, then have the query accessor consume it. Do not extract an options factory when one feature query is its only caller; retain reusable options only when an independent prefetch or loader also consumes them.

Load infinite-query pages incrementally; do not fetch every page to imitate server filtering or sorting. If a later page fails, keep resolved pages visible and retry the failed page.
