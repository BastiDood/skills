# Query Function Inputs

When a value is in `queryKey`, the query function reads it from the context's `queryKey`, never from the closure. Receive cancellation from `context.signal` and an infinite-query cursor from `context.pageParam`.

```typescript
import { infiniteQueryOptions, queryOptions } from '@tanstack/svelte-query';

interface ItemPage {
	items: string[];
	nextCursor: number | undefined;
}

declare function fetchItem(id: string, signal: AbortSignal): Promise<string>;
declare function fetchItemPage(
	categoryId: string,
	cursor: number,
	signal: AbortSignal,
): Promise<ItemPage>;

export const itemQueries = {
	// BAD: the key contains the ID, but the query function reads the closure's copy.
	capturedDetail: (itemId: string) =>
		queryOptions({
			queryKey: ['items', 'detail', itemId] as const,
			queryFn: async ({ signal }) => await fetchItem(itemId, signal),
		}),
	// GOOD: the request reads the ID from the key it is cached under.
	detail: (itemId: string) =>
		queryOptions({
			queryKey: ['items', 'detail', itemId] as const,
			queryFn: async ({ queryKey: [, , keyItemId], signal }) => await fetchItem(keyItemId, signal),
		}),
	// GOOD: the cursor comes from `pageParam`, so every page shares one key.
	feed: (categoryId: string) =>
		infiniteQueryOptions({
			queryKey: ['items', 'feed', categoryId] as const,
			initialPageParam: 0,
			queryFn: async ({ queryKey: [, , keyCategoryId], pageParam, signal }) =>
				await fetchItemPage(keyCategoryId, pageParam, signal),
			getNextPageParam: lastPage => lastPage.nextCursor,
		}),
};
```

A component consumes these through accessors:

```svelte
<script lang="ts">
	import { createInfiniteQuery, createQuery } from '@tanstack/svelte-query';

	import { itemQueries } from './item-queries';

	interface Props {
		itemId: string;
		categoryId: string;
	}

	const { itemId, categoryId }: Props = $props();

	// Each accessor reads the current props, so a changed prop produces a new key.
	const item = createQuery(() => itemQueries.detail(itemId));
	const feed = createInfiniteQuery(() => itemQueries.feed(categoryId));
</script>
```

Avoid captures where possible, but they are not banned. A captured value that does not change the response, such as an API client or a stable helper function, stays in the closure. Every captured value that changes the response must be in the key; otherwise requests with different responses share one cache entry. Once it is in the key, read it from there.

Do not hide response-changing arguments in `meta`, module state, ambient configuration, or an imported helper's closure. Do not put secrets, functions, clients, or `AbortSignal` instances in a key. Put an explicit domain timestamp or date range in the key when it changes the response.
