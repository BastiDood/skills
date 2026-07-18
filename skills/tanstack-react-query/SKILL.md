---
name: tanstack-react-query
description: Data fetching with `TanStack React Query` using the Loader/Inner pattern. Use when fetching data, managing cache, or implementing mutations in React.
---

# TanStack React Query

Data fetching patterns with `TanStack Query` for React.

## Documentation

- If available, use Context7 (Library ID: `/tanstack/query`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `TanStack/query`) for implementation details.

## Loader/Inner Pattern

Separate data loading from presentation for testability:

```tsx
// Inner: pure presentation, receives resolved data
function JobDetailInner({ data }: { data: JobData }) {
	const processed = useMemo(() => data.items.map(transform), [data.items]);
	return <DataDisplay items={processed} />;
}

// Loader: handles query states
function JobDetailLoader({ jobId }: { jobId: string }) {
	const { data, isPending, isError, error } = useGetJob(jobId);

	if (isPending) return <Skeleton />;
	if (isError) return <ErrorBanner error={error} />;
	if (typeof data === 'undefined') return <NotFound />;

	return <JobDetailInner data={data} />;
}
```

## Query Key Factory

```typescript
export const jobQueryKeys = {
	all: ['jobs'] as const,
	list: (filters?: JobFilters) =>
		filters ? (['jobs', 'list', filters] as const) : (['jobs', 'list'] as const),
	job: (id: string) => ['jobs', 'detail', id] as const,
};
```

## Query Hook Pattern

Prefer accessing variables from callback context rather than closures to avoid stale data:

```typescript
import { skipToken, useQuery } from '@tanstack/react-query';

export function useGetJob(jobId: string | undefined) {
	return useQuery({
		queryKey: jobQueryKeys.job(jobId ?? ''),
		queryFn: jobId ? ({ queryKey: [, , id] }) => fetchJob(id) : skipToken,
	});
}

// Usage: destructure stable properties, not the unstable object
const { data, isPending, isError, error } = useGetJob(jobId);
```

**Why callbacks over closures:**

- Closures capture values at creation time, risking stale data
- Callback parameters provide fresh values on each execution
- Eliminates need for `useCallback` memoization
- Runtime guards ensure type safety without non-null assertions

## Mutation Pattern

Pass dynamic values through mutation variables, not hook parameters:

```typescript
interface CreateJobVariables {
	data: CreateJobRequest;
}

export function useCreateJob() {
	return useMutation({
		async mutationFn({ data }: CreateJobVariables) {
			return await createJob(data);
		},
	});
}
```

### Cache Invalidation in Callbacks

Use `context.client` from callback parameters — never call `useQueryClient()` inside callbacks:

```typescript
export function useUpdateJob(jobId: string) {
	return useMutation({
		async mutationFn({ data }: { data: UpdateJobRequest }) {
			return await updateJob(jobId, data);
		},
		onSuccess(data, variables, onMutateResult, context) {
			context.client.invalidateQueries({ queryKey: jobQueryKeys.job(jobId) });
		},
	});
}
```

## Status-Driven Polling

```typescript
const { data } = useQuery({
	queryKey: jobQueryKeys.job(jobId),
	async queryFn({ queryKey: [, , id] }) {
		return await fetchJob(id);
	},
	refetchInterval({ state }) {
		const status = state.data?.status;
		switch (status) {
			case 'PENDING':
			case 'PROCESSING':
				return 5000;
			default:
				return false;
		}
	},
});
```
