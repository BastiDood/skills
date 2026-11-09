# Request Ownership

Let TanStack Query own cache identity, cancellation, retries, deduplication, and pending/error state. An effect plus local state duplicates that lifecycle and makes the view responsible for request coordination.

```tsx
import { startTransition, useEffect, useState } from 'react';
import { queryOptions, useQuery } from '@tanstack/react-query';

async function fetchTeamReport(teamId: string, signal: AbortSignal) {
	const response = await fetch('/api/team-report/' + encodeURIComponent(teamId), { signal });
	if (!response.ok) throw new Error('Team report request failed');
	return await response.text();
}

// BAD: the component recreates the query lifecycle.
function BadTeamReport({ teamId }: { teamId: string }) {
	const [report, setReport] = useState<string>();
	useEffect(() => {
		const controller = new AbortController();
		const { signal } = controller;
		startTransition(async () => {
			try {
				setReport(await fetchTeamReport(teamId, signal));
			} catch {
				setReport('Report unavailable');
			}
		});
		return () => controller.abort();
	}, [teamId]);
	return <p>{report ?? 'Loading report'}</p>;
}

// GOOD: the query factory defines the operation. TanStack Query runs it.
const teamQueries = {
	report: (teamId: string) =>
		queryOptions({
			queryKey: ['teams', 'report', teamId] as const,
			queryFn: async ({ queryKey: [, , keyTeamId], signal }) => {
				return await fetchTeamReport(keyTeamId, signal);
			},
		}),
};

function TeamReport({ teamId }: { teamId: string }) {
	const { data, isPending, isError, error } = useQuery(teamQueries.report(teamId));
	if (isPending) return <p>Loading report</p>;
	if (isError) return <p role="alert">{error.message}</p>;
	return <p>{data}</p>;
}
```

The query factory owns request normalization, key, query function, and per-query options. The component owns only the invocation and the status branches it renders.

For an infinite query, the factory owns page acquisition and continuation through `infiniteQueryOptions()`. Do not fetch every page to imitate server filtering or sorting. A later-page failure leaves resolved pages visible and is retried at the page-loading boundary.

## Imperative Cache Access

Do not call `useQueryClient()` in components to fetch, read, write, or invalidate cache entries; let `useQuery` own reads and mutation definitions own writes through `context.client`. Two cases are allowed because no hook expresses them:

- Prefetch on hover or focus to start the request before navigation needs it.
- Ensure data in an event handler to return cached data or fetch it once before the handler acts.

```tsx
import { startTransition } from 'react';
import { queryOptions, useQueryClient } from '@tanstack/react-query';

interface Job {
	id: string;
	title: string;
}

declare function fetchJob(id: string, signal: AbortSignal): Promise<Job>;
declare function openEditor(job: Job): void;
declare function showLoadError(error: unknown): void;

const jobQueries = {
	detail: (id: string) =>
		queryOptions({
			queryKey: ['jobs', 'detail', id] as const,
			queryFn: async ({ queryKey: [, , keyId], signal }) => await fetchJob(keyId, signal),
		}),
};

function JobLink({ jobId }: { jobId: string }) {
	const queryClient = useQueryClient();

	function prefetchJob() {
		startTransition(async () => {
			try {
				await queryClient.query(jobQueries.detail(jobId));
			} catch {
				// A failed prefetch stays silent. The query that renders the job reports the error.
			}
		});
	}

	function editJob() {
		startTransition(async () => {
			let job: Job;
			try {
				// `staleTime: 'static'` returns cached data whenever it exists.
				job = await queryClient.query({ ...jobQueries.detail(jobId), staleTime: 'static' });
			} catch (error) {
				showLoadError(error);
				return;
			}
			openEditor(job);
		});
	}

	return (
		<button type="button" onMouseEnter={prefetchJob} onFocus={prefetchJob} onClick={editJob}>
			Edit job
		</button>
	);
}
```

Use `queryClient.query()` for both cases, never `prefetchQuery()` or `ensureQueryData()`. A prefetch catches and ignores its error. An ensure sets `staleTime: 'static'`. Wrap only the `query()` call in each `try` block to keep errors from the follow-up action out of the load-failure path.
