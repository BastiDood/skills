# Request Ownership

Load reactive server data through TanStack Query. A query factory owns input normalization, cache identity, request construction, and per-query options. The component's `createQuery` accessor supplies the current inputs. TanStack Query owns pending state, retries, cancellation, and request deduplication.

Do not duplicate the same server-data request in a mount effect or event handler. Change the state represented in the query key, invalidate the affected key, or invoke the query API's explicit refetch operation.

```typescript
// item-queries.ts
import { queryOptions } from '@tanstack/svelte-query';

export interface Item {
	id: string;
	name: string;
}

function normalizeItemFilter(filter: string) {
	return filter.trim().toLowerCase();
}

export async function fetchItems(filter: string, signal: AbortSignal) {
	const response = await fetch('/api/items?filter=' + encodeURIComponent(filter), { signal });
	if (!response.ok) throw new Error('Items request failed');
	return await response.text();
}

declare function fetchItem(id: string, signal: AbortSignal): Promise<Item>;

export const itemQueries = {
	all: () => ['items'] as const,
	list: (filter: string) =>
		queryOptions({
			queryKey: ['items', 'list', normalizeItemFilter(filter)] as const,
			queryFn: async ({ queryKey: [, , keyFilter], signal }) => await fetchItems(keyFilter, signal),
		}),
	detail: (id: string) =>
		queryOptions({
			queryKey: ['items', 'detail', id] as const,
			queryFn: async ({ queryKey: [, , keyId], signal }) => await fetchItem(keyId, signal),
		}),
};
```

```svelte
<script lang="ts">
	import { createQuery } from '@tanstack/svelte-query';

	import { fetchItems, itemQueries } from './item-queries';

	let filter = $state('active');
	const query = createQuery(() => itemQueries.list(filter));

	// BAD: duplicates the query-owned request
	async function handleFilterChangeWithFetch(next: string) {
		filter = next;
		const { signal } = new AbortController();
		await fetchItems(next, signal);
	}

	// GOOD: the key-driving state change lets the query own the request
	function handleFilterChange(next: string) {
		filter = next;
	}
</script>
```

Keep query factories in an ordinary `.ts` module; they take plain values and need no runes. Read reactive inputs inside the `createQuery` accessor. Cache an expensive non-constant derivation with `$derived` in the component, then pass its value to the factory.

Load infinite-query pages incrementally through an `infiniteQueryOptions()` factory; do not fetch every page to imitate server filtering or sorting. If a later page fails, keep resolved pages visible and retry the failed page.

## Imperative Cache Access

Do not call `useQueryClient()` in components to fetch, read, write, or invalidate cache entries; let `createQuery` own reads and mutation definitions own writes through `context.client`. Two cases are allowed because no reactive primitive expresses them:

- Prefetch on hover or focus to start the request before navigation needs it.
- Ensure data in an event handler to return cached data or fetch it once before the handler acts.

```svelte
<script lang="ts">
	import { useQueryClient } from '@tanstack/svelte-query';

	import { itemQueries, type Item } from './item-queries';

	interface Props {
		itemId: string;
		openEditor: (item: Item) => void;
		showLoadError: (error: unknown) => void;
	}

	const { itemId, openEditor, showLoadError }: Props = $props();
	const queryClient = useQueryClient();

	async function prefetchItem() {
		try {
			await queryClient.query(itemQueries.detail(itemId));
		} catch {
			// A failed prefetch stays silent. The query that renders the item reports the error.
		}
	}

	async function editItem() {
		let item: Item;
		try {
			// `staleTime: 'static'` returns cached data whenever it exists.
			item = await queryClient.query({ ...itemQueries.detail(itemId), staleTime: 'static' });
		} catch (error) {
			showLoadError(error);
			return;
		}
		openEditor(item);
	}
</script>

<button type="button" onmouseenter={prefetchItem} onfocus={prefetchItem} onclick={editItem}>
	Edit item
</button>
```

Use `queryClient.query()` for both cases, never `prefetchQuery()` or `ensureQueryData()`. A prefetch catches and ignores its error. An ensure sets `staleTime: 'static'`. Wrap only the `query()` call in each `try` block to keep errors from the follow-up action out of the load-failure path.
