---
name: tanstack-react-query-best-practices
description: Opinionated TanStack Query conventions for React covering the Loader/Inner pattern, query hooks, and cache invalidation. Use when fetching data or implementing mutations in React.
---

# TanStack React Query Best Practices

Opinionated conventions for TanStack Query in React. API documentation lives elsewhere (see the footer); every section here is a rule.

## Loader/Inner Pattern

Separate data loading from presentation. The Inner component receives resolved data and stays pure (testable without a QueryClient); the Loader owns the query states.

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

Centralize query keys in one factory per entity so query definitions and invalidation targets can never drift apart:

```typescript
export const jobQueryKeys = {
	all: ['jobs'] as const,
	list: (filters?: JobFilters) =>
		filters ? (['jobs', 'list', filters] as const) : (['jobs', 'list'] as const),
	job: (id: string) => ['jobs', 'detail', id] as const,
};
```

## Callbacks over Closures in Query Functions

Closures capture values at hook creation and go stale; callback parameters are fresh on every execution and need no `useCallback`. Model the disabled case with `skipToken` so the runtime guard doubles as the type guard.

```typescript
import { skipToken, useQuery } from '@tanstack/react-query';

// BAD - closure captures jobId and papers over undefined with an assertion
export function useGetJob(jobId: string | undefined) {
	return useQuery({
		queryKey: jobQueryKeys.job(jobId ?? ''),
		queryFn: () => fetchJob(jobId!),
	});
}

// GOOD - skipToken models the disabled case; the callback reads fresh values
export function useGetJob(jobId: string | undefined) {
	return useQuery({
		queryKey: jobQueryKeys.job(jobId ?? ''),
		queryFn: jobId ? ({ queryKey: [, , id] }) => fetchJob(id) : skipToken,
	});
}
```

At call sites, destructure the stable properties (`data`, `isPending`, `isError`, `error`), not the unstable result object.

## Mutation Variables over Hook Parameters

Pass dynamic values through mutation variables, not hook parameters. Hook parameters are closures with the same staleness problem as above; variables flow through `mutate(...)` fresh on every call.

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

## Invalidate via `context.client`

Mutation callbacks receive a context exposing the QueryClient (TanStack Query >= 5.89). Use `context.client` from the callback parameters; never call `useQueryClient()` inside callbacks.

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

## Documentation

- If available, use Context7 (Library ID: `/tanstack/query`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `TanStack/query`) for implementation details.
