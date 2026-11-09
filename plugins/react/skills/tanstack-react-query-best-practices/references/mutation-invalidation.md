# Mutation Invalidation

Invalidate the cache entry identified by successful mutation variables, from the mutation definition's `onSuccess`, through the callback context's `QueryClient`. Await or return the `invalidateQueries` promise to keep the mutation pending until the refetch completes. TanStack Query awaits a promise returned from `onSuccess`.

```typescript
import { queryOptions, useMutation } from '@tanstack/react-query';

interface Job {
	id: string;
	title: string;
}

declare function fetchJob(id: string, signal: AbortSignal): Promise<Job>;
declare function updateJob(job: Job): Promise<Job>;

const jobQueries = {
	all: () => ['jobs'] as const,
	detail: (id: string) =>
		queryOptions({
			queryKey: ['jobs', 'detail', id] as const,
			queryFn: async ({ queryKey: [, , keyId], signal }) => await fetchJob(keyId, signal),
		}),
};

// BAD: the floating promise lets the mutation settle before the refetch. Every edit invalidates unrelated records.
function useBroadUpdateJob() {
	return useMutation({
		mutationFn: updateJob,
		onSuccess(_data, _variables, _onMutateResult, context) {
			context.client.invalidateQueries({ queryKey: jobQueries.all() });
		},
	});
}

// GOOD: mutation variables identify the stale entry. The mutation stays pending until it refetches.
export function useUpdateJob() {
	return useMutation({
		mutationFn: updateJob,
		async onSuccess(_data, variables, _onMutateResult, context) {
			const { queryKey } = jobQueries.detail(variables.id);
			await context.client.invalidateQueries({ queryKey });
		},
	});
}
```

Use `context.client`, the fourth callback parameter, from mutation callbacks. Do not call `useQueryClient()` to reach the client inside a callback.

Keep cache policy in the mutation definition so it runs for every invocation. Keep UI consequences such as closing a dialog in per-call callbacks at the `mutate` call site.

Invalidate the narrowest query family that has become stale. Invalidate a list as well only when the mutation changes list membership, ordering, or displayed summary data.

Keep optimistic updates and other application-specific sequencing with the application feature that owns their policy.
