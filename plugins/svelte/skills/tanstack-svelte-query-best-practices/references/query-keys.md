# Cache Identity

Every application input that can change a query response belongs in its query key. Keep the key with its capability; extract a shared factory only when fetching, prefetching, or invalidation uses the same hierarchy.

```typescript
// BAD: distinct filters reuse one cache entry.
const queryKey = ['items', 'list'] as const;

// GOOD: every response-changing input participates in identity.
export const itemQueryKeys = {
	all: ['items'] as const,
	list: (filters: ItemFilters | undefined) =>
		typeof filters === 'undefined'
			? (['items', 'list'] as const)
			: (['items', 'list', filters] as const),
	detail: (id: string | undefined) => ['items', 'detail', id] as const,
};
```

Include filters, sorting, locale, tenant, page size, and other response-changing inputs. A paginated query includes its page in the key. An infinite query receives its cursor through `pageParam`, so its pages form one keyed result.

Construct the request from `queryFn` context's `queryKey`, never captured copies. Put a second identity in a cursor only when it verifies a named compatibility invariant; it does not prove pagination correctness or efficiency. Keep presentation-only values outside the key and query function. Never include tokens, API keys, cookies, authorization codes, other credentials, functions, clients, or `AbortSignal` instances in a key.

Use readonly literal tuples and one hierarchy so broad invalidation can target an entity prefix while detailed invalidation targets one record. Review cache identity whenever a request gains a response-changing argument.
