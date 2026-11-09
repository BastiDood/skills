# Absent Identifiers and Defaulted Key Values

Never default a value that flows into a query key. This covers `??` and `||` fallbacks, default parameters, and destructuring defaults whose binding reaches the key. A default fabricates request input: the cache stores a real response under an identity the caller never chose, making absence indistinguishable from an explicit value.

```typescript
import { queryOptions } from '@tanstack/react-query';

interface JobListInput {
	status: 'open' | 'closed';
	page?: number;
}

declare function fetchTeamReport(teamId: string, signal: AbortSignal): Promise<string>;
declare function fetchJobs(
	status: 'open' | 'closed',
	page: number,
	signal: AbortSignal,
): Promise<string[]>;

export const badQueries = {
	// BAD: absence becomes a request for a fabricated record.
	report: (teamId: string | undefined) =>
		queryOptions({
			queryKey: ['teams', 'report', teamId ?? ''] as const,
			queryFn: async ({ queryKey: [, , keyTeamId], signal }) =>
				await fetchTeamReport(keyTeamId, signal),
		}),
	// BAD: a destructuring default invents the page the caller omitted.
	jobs: ({ status, page = 1 }: JobListInput) =>
		queryOptions({
			queryKey: ['jobs', 'list', status, page] as const,
			queryFn: async ({ queryKey: [, , keyStatus, keyPage], signal }) =>
				await fetchJobs(keyStatus, keyPage, signal),
		}),
};
```

Resolve a required input at the boundary that owns it, such as a parsed route schema or a required prop. Give the factory a required parameter. When an identifier is genuinely absent, preserve the absence in the key and select `skipToken`.

```typescript
import { queryOptions, skipToken } from '@tanstack/react-query';

declare function fetchTeamReport(teamId: string, signal: AbortSignal): Promise<string>;

export const teamQueries = {
	// GOOD: absence stays in the key and no request runs.
	report: (teamId: string | undefined) =>
		queryOptions({
			queryKey: ['teams', 'report', teamId] as const,
			queryFn:
				typeof teamId === 'undefined'
					? skipToken
					: async ({ queryKey: [, , keyTeamId], signal }) => {
							if (typeof keyTeamId === 'undefined') throw new Error('Expected a team ID');
							return await fetchTeamReport(keyTeamId, signal);
						},
		}),
};
```

The key preserves the optional identifier's type. Selecting `skipToken` does not narrow the context value. The query function guards its own ID before constructing the request. This avoids a cast, non-null assertion, or fake default. Call the factory unconditionally:

```tsx
import { useQuery } from '@tanstack/react-query';

function TeamReport({ teamId }: { teamId: string | undefined }) {
	// GOOD: the hook always runs. `skipToken` keeps the request idle while `teamId` is absent.
	const { data, isPending, isError, error } = useQuery(teamQueries.report(teamId));
	if (typeof teamId === 'undefined') return <p>Select a team</p>;
	if (isPending) return <p>Loading report</p>;
	if (isError) return <p role="alert">{error.message}</p>;
	return <p>{data}</p>;
}
```

Prefer `skipToken` over `enabled: false`: the conditional `queryFn` narrows the identifier's type without a non-null assertion. Calling `refetch()` on a query that uses `skipToken` throws a `Missing queryFn` error. Model a manually triggered request explicitly instead of calling `refetch()` on a disabled query.

The disabled state is part of the query contract, not a request for an empty record. Model `null` separately when the domain distinguishes it from `undefined`.
