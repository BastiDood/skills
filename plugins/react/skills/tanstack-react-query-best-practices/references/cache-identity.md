# Cache Identity

Every application input that can change a query response belongs in its query key. Build the key inside the entity's `queryOptions()` factory to make fetching, prefetching, cache reads, and invalidation all address the same hierarchy.

```typescript
import { queryOptions } from '@tanstack/react-query';

interface Job {
	id: string;
}

interface JobFilters {
	status: 'open' | 'closed';
	locale: string;
}

declare function fetchJobs(filters: JobFilters, signal: AbortSignal): Promise<Job[]>;

export const jobQueries = {
	all: () => ['jobs'] as const,
	// BAD: `locale` changes the response but is missing from the key. Every locale shares one entry.
	partialList: (filters: JobFilters) =>
		queryOptions({
			queryKey: ['jobs', 'list', filters.status] as const,
			queryFn: async ({ queryKey: [, , keyStatus], signal }) =>
				await fetchJobs({ status: keyStatus, locale: filters.locale }, signal),
		}),
	// GOOD: every response-changing input participates in identity.
	list: (filters: JobFilters) =>
		queryOptions({
			queryKey: ['jobs', 'list', filters] as const,
			queryFn: async ({ queryKey: [, , keyFilters], signal }) =>
				await fetchJobs(keyFilters, signal),
		}),
};
```

The key defines when results can be reused. Include filters, sorting, locale, tenant, page size, and every other response-changing setting. A separate paginated query includes its page in the key; an infinite query receives its cursor through `QueryFunctionContext.pageParam` so all of its pages form one keyed result.

Put a second identity in a cursor only when it verifies a named compatibility invariant; it does not prove pagination correctness or efficiency.

Do not include values that affect only rendering after the response arrives. Do not create separate key shapes for fetching and invalidation: invalidate one record with the `queryKey` destructured from its factory's options, and an entity family through a prefix such as `jobQueries.all()`.

Use `readonly` literal tuples so keys preserve their exact structure. `as const` is appropriate here for literal cache-key constants, not as a cast over uncertain data.

Review cache identity whenever a request gains a new argument that can change the returned data.
