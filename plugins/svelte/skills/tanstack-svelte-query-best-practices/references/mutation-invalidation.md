# Mutation Invalidation

Invalidate the cache entry identified by successful mutation variables. Use the callback context's `QueryClient`.

Within component setup, the key hierarchy makes the affected record explicit:

```typescript
import { createMutation, queryOptions } from '@tanstack/svelte-query';

interface Job {
	id: string;
	title: string;
}

declare function getJob(id: string): Promise<Job>;
declare function updateJob(job: Job): Promise<Job>;

const jobQueryKeys = {
	all: ['jobs'] as const,
	detail(id: string) {
		return ['jobs', 'detail', id] as const;
	},
};

function jobOptions(id: string) {
	return queryOptions({
		queryKey: jobQueryKeys.detail(id),
		queryFn: async ({ queryKey: [, , jobId] }) => await getJob(jobId),
	});
}

// BAD: every successful edit invalidates unrelated records.
const broadMutation = createMutation(() => ({
	mutationFn: updateJob,
	async onSuccess(_data, _variables, _onMutateResult, context) {
		await context.client.invalidateQueries({ queryKey: jobQueryKeys.all });
	},
}));

// GOOD: mutation variables identify the exact stale cache entry.
const mutation = createMutation(() => ({
	mutationFn: updateJob,
	async onSuccess(_data, variables, _onMutateResult, context) {
		await context.client.invalidateQueries({ queryKey: jobQueryKeys.detail(variables.id) });
	},
}));
```

Invalidate the narrowest query family that became stale. Invalidate a list only when the mutation changes list membership, ordering, or displayed summary data.

Do not acquire a client from component scope inside the callback. Keep optimistic-update sequencing in the owning project when its policy does not generalize.
