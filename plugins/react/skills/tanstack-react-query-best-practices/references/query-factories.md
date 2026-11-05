# Query Factories

Define each entity's queries as `queryOptions()` factories grouped in one object. A factory returns the key, query function, and per-query options together, so `useQuery`, `useSuspenseQuery`, prefetching, cache reads, and invalidation share one definition. The same object owns key prefixes for broad invalidation; do not keep a separate key-factory object beside it.

```typescript
import { queryOptions } from '@tanstack/react-query';

interface Job {
	id: string;
	title: string;
}

interface JobFilters {
	status: 'open' | 'closed';
}

declare function fetchJobs(filters: JobFilters, signal: AbortSignal): Promise<Job[]>;
declare function fetchJob(id: string, signal: AbortSignal): Promise<Job>;

export const jobQueries = {
	all: () => ['jobs'] as const,
	lists: () => ['jobs', 'list'] as const,
	list: (filters: JobFilters) =>
		queryOptions({
			queryKey: ['jobs', 'list', filters] as const,
			queryFn: async ({ queryKey: [, , keyFilters], signal }) =>
				await fetchJobs(keyFilters, signal),
		}),
	detail: (id: string) =>
		queryOptions({
			queryKey: ['jobs', 'detail', id] as const,
			queryFn: async ({ queryKey: [, , keyId], signal }) => await fetchJob(keyId, signal),
		}),
};
```

Consume the factory directly. A custom hook whose only job is to forward a key and query function to `useQuery` confines the definition to React components and separates it from the imperative `QueryClient` APIs.

```tsx
import { useQuery } from '@tanstack/react-query';

// BAD: the hook hides a reusable definition behind a component-only boundary.
function useJob(id: string) {
	return useQuery({
		queryKey: ['jobs', 'detail', id] as const,
		queryFn: async ({ queryKey: [, , keyId], signal }) => await fetchJob(keyId, signal),
	});
}

// GOOD: the component consumes the shared definition.
function JobTitle({ jobId }: { jobId: string }) {
	const { data, isPending, isError, error } = useQuery(jobQueries.detail(jobId));
	if (isPending) return <p>Loading job</p>;
	if (isError) return <p role="alert">{error.message}</p>;
	return <h2>{data.title}</h2>;
}
```

Address cached data through the factory's `queryKey`, and spread the factory to add observer-specific options.

```tsx
import { type QueryClient, useQuery } from '@tanstack/react-query';

function readCachedJob(queryClient: QueryClient, jobId: string) {
	// The factory's key carries the data type, so this returns `Job | undefined` without a type argument.
	const { queryKey } = jobQueries.detail(jobId);
	return queryClient.getQueryData(queryKey);
}

function JobHeading({ jobId }: { jobId: string }) {
	// Spread the factory to add observer-specific options such as `select` or `enabled`.
	const { data: title } = useQuery({ ...jobQueries.detail(jobId), select: job => job.title });
	return <h2>{title}</h2>;
}
```

Never retype a key literal at a call site; a retyped literal drifts from the factory and loses the data type. Write a custom hook only when it adds behavior beyond `useQuery`, such as composing several hooks or reading router state, and have it spread the factory.

Spell each key literally inside the factory. The prefixes are plain tuples for `invalidateQueries`, not queries; every query key starts with the prefix of the family it belongs to.
