# Query Factories

Define each entity's queries as `queryOptions()` factories grouped in one object. A factory returns the key, query function, and per-query options together to give `createQuery`, prefetching, cache reads, and invalidation one shared definition. The same object owns key prefixes for broad invalidation; do not keep a separate key-factory object beside it.

```typescript
// job-queries.ts
import { queryOptions } from '@tanstack/svelte-query';

import { fetchJob, fetchJobs, type JobFilters } from './job-api';

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

As plain functions of plain values, factories live in an ordinary `.ts` module without runes. Reactivity comes from the accessor passed to `createQuery`: it reads the current reactive inputs and calls the factory to produce new options for a changed input.

```svelte
<script lang="ts">
	import { createQuery } from '@tanstack/svelte-query';

	import { fetchJob } from './job-api';
	import { jobQueries } from './job-queries';

	interface Props {
		jobId: string;
	}

	const { jobId }: Props = $props();

	// BAD: the key and query function are defined inline, out of reach of prefetching and invalidation.
	const inlineQuery = createQuery(() => ({
		queryKey: ['jobs', 'detail', jobId] as const,
		queryFn: async ({ queryKey: [, , keyId], signal }) => await fetchJob(keyId, signal),
	}));

	// GOOD: the accessor reads the current prop and calls the shared factory.
	const query = createQuery(() => jobQueries.detail(jobId));

	// GOOD: spread the factory inside the accessor to add observer-specific options such as `select` or `enabled`.
	const titleQuery = createQuery(() => ({ ...jobQueries.detail(jobId), select: job => job.title }));
</script>
```

Do not wrap `createQuery` in a feature function whose only job is to apply one factory; call the factory in the accessor.

Address cached data through the factory's `queryKey`. Never retype a key literal at a call site; a retyped literal drifts from the factory and loses the data type.

```typescript
import type { QueryClient } from '@tanstack/svelte-query';

import { jobQueries } from './job-queries';

export function readCachedJob(queryClient: QueryClient, jobId: string) {
	// With the data type carried by the factory's key, this read needs no type argument.
	const { queryKey } = jobQueries.detail(jobId);
	return queryClient.getQueryData(queryKey);
}
```

Spell each key literally inside the factory. The prefixes are plain tuples for `invalidateQueries`, not queries; every query key starts with the prefix of the family it belongs to.
