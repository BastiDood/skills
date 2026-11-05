# Cache Identity

Every application input that can change a query response belongs in its query key. Build the key inside the entity's `queryOptions()` factory, so fetching, prefetching, cache reads, and invalidation all address the same hierarchy.

```typescript
import { queryOptions } from '@tanstack/svelte-query';

interface ItemFilters {
	status: 'active' | 'archived';
	locale: string;
}

declare function fetchItems(filters: ItemFilters, signal: AbortSignal): Promise<string[]>;

export const itemQueries = {
	all: () => ['items'] as const,
	// BAD: `locale` changes the response but is missing from the key, so every locale shares one entry.
	partialList: (filters: ItemFilters) =>
		queryOptions({
			queryKey: ['items', 'list', filters.status] as const,
			queryFn: async ({ queryKey: [, , keyStatus], signal }) =>
				await fetchItems({ status: keyStatus, locale: filters.locale }, signal),
		}),
	// GOOD: every response-changing input participates in identity.
	list: (filters: ItemFilters) =>
		queryOptions({
			queryKey: ['items', 'list', filters] as const,
			queryFn: async ({ queryKey: [, , keyFilters], signal }) =>
				await fetchItems(keyFilters, signal),
		}),
};
```

Include filters, sorting, locale, tenant, page size, and other response-changing inputs. A paginated query includes its page in the key. An infinite query receives its cursor through `pageParam`, so its pages form one keyed result.

Put a second identity in a cursor only when it verifies a named compatibility invariant; it does not prove pagination correctness or efficiency. Keep presentation-only values outside the key and query function. Never include tokens, API keys, cookies, authorization codes, other credentials, functions, clients, or `AbortSignal` instances in a key.

Use readonly literal tuples and one hierarchy so broad invalidation can target an entity prefix such as `itemQueries.all()` while detailed invalidation targets one record with the `queryKey` destructured from its factory's options. Review cache identity whenever a request gains a response-changing argument.
