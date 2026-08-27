# Query Function Inputs

Do not capture application request variables in `queryFn`, even when the key contains the same values. Put every request input in the typed `queryKey`, then destructure it from the query-function context. Receive cancellation from `context.signal` and an infinite-query cursor from `context.pageParam`.

Declare every `queryFn` as `async` and `await` its request.

In this component excerpt, `itemId` is a reactive `string`; `fetchItem(id, signal)` requests that item. Imports and prop declarations are omitted.

```svelte
<script lang="ts">
	// BAD: the key contains the ID, but the request captures it.
	const capturedQuery = createQuery(() => ({
		queryKey: ['items', 'detail', itemId] as const,
		queryFn: async ({ signal }) => await fetchItem(itemId, signal),
	}));

	// GOOD: the request reads the ID from context.
	const query = createQuery(() => ({
		queryKey: ['items', 'detail', itemId] as const,
		queryFn: async ({ queryKey: [, , keyItemId], signal }) => {
			return await fetchItem(keyItemId, signal);
		},
	}));
</script>
```

For infinite queries, the cursor comes from `pageParam`. Here, `categoryId` is a reactive `string`; `fetchItemPage(categoryId, cursor, signal)` returns a page whose `nextCursor` is a number, or `undefined` when there is no next page. Imports and input declarations are omitted.

```typescript
const query = createInfiniteQuery(() => ({
	queryKey: ['items', 'feed', categoryId] as const,
	initialPageParam: 0,
	queryFn: async ({ queryKey: [, , keyCategoryId], pageParam, signal }) => {
		return await fetchItemPage(keyCategoryId, pageParam, signal);
	},
	getNextPageParam: lastPage => lastPage.nextCursor,
}));
```

Do not hide response-changing arguments in `meta`, module state, ambient configuration, or an imported helper's closure. Do not put secrets, functions, clients, or `AbortSignal` instances in a key. Put an explicit domain timestamp or date range in the key when it changes the response.
