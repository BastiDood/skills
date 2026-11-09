# Absent Identifiers and Defaulted Key Values

Never default a value that flows into a query key. This covers `??` and `||` fallbacks, default parameters, and destructuring defaults whose binding reaches the key. A default fabricates request input: the cache stores a real response under an identity the caller never chose, making absence indistinguishable from an explicit value.

```typescript
import { queryOptions } from '@tanstack/svelte-query';

interface JobListInput {
	status: 'open' | 'closed';
	page?: number;
}

declare function fetchJob(id: string, signal: AbortSignal): Promise<string>;
declare function fetchJobs(
	status: 'open' | 'closed',
	page: number,
	signal: AbortSignal,
): Promise<string[]>;

export const badQueries = {
	// BAD: absence becomes a request for a fabricated record.
	detail: (jobId: string | undefined) =>
		queryOptions({
			queryKey: ['jobs', 'detail', jobId ?? ''] as const,
			queryFn: async ({ queryKey: [, , keyJobId], signal }) => await fetchJob(keyJobId, signal),
		}),
	// BAD: a destructuring default invents the page the caller omitted.
	list: ({ status, page = 1 }: JobListInput) =>
		queryOptions({
			queryKey: ['jobs', 'list', status, page] as const,
			queryFn: async ({ queryKey: [, , keyStatus, keyPage], signal }) =>
				await fetchJobs(keyStatus, keyPage, signal),
		}),
};
```

Resolve a required input at the boundary that owns it, such as a parsed route schema or a required prop. Give the factory a required parameter. When an identifier is genuinely absent, preserve the absence in the key and select `skipToken`.

```typescript
import { queryOptions, skipToken } from '@tanstack/svelte-query';

declare function fetchJob(id: string, signal: AbortSignal): Promise<string>;

export const jobQueries = {
	// GOOD: absence stays in the key and no request runs.
	optionalDetail: (jobId: string | undefined) =>
		queryOptions({
			queryKey: ['jobs', 'detail', jobId] as const,
			queryFn:
				typeof jobId === 'undefined'
					? skipToken
					: async ({ queryKey: [, , keyJobId], signal }) => {
							if (typeof keyJobId === 'undefined') throw new Error('Expected a job ID');
							return await fetchJob(keyJobId, signal);
						},
		}),
};
```

Choosing `skipToken` from the factory's argument does not narrow the optional ID in the query-function context. Guard the key-derived ID before the request. This avoids a cast, non-null assertion, or fake default. Create the query unconditionally:

```svelte
<script lang="ts">
	import { createQuery } from '@tanstack/svelte-query';

	import { jobQueries } from './job-queries';

	interface Props {
		jobId: string | undefined;
	}

	const { jobId }: Props = $props();

	// GOOD: the query always exists. `skipToken` keeps the request idle while `jobId` is absent.
	const query = createQuery(() => jobQueries.optionalDetail(jobId));
</script>
```

Prefer `skipToken` over `enabled: false`: the conditional `queryFn` narrows the identifier's type without a non-null assertion. Calling `refetch()` on a query that uses `skipToken` throws a `Missing queryFn` error. Model a manually triggered request explicitly instead of calling `refetch()` on a disabled query.

Do not replace absence with `''`, `0`, a sentinel UUID, or another fabricated request value. Model `null` separately when the domain distinguishes it from `undefined`.
