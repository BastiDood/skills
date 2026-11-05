# Query Function Inputs

When a value is in `queryKey`, the query function reads it from the context's `queryKey`, never from the closure. Receive cancellation through `context.signal` and an infinite-query cursor through `context.pageParam`.

```typescript
import { queryOptions } from '@tanstack/react-query';

declare function fetchReport(id: string, signal: AbortSignal): Promise<string>;

export const reportQueries = {
	// BAD: the key contains the ID, but the query function reads the closure's copy.
	capturedDetail: (reportId: string) =>
		queryOptions({
			queryKey: ['reports', 'detail', reportId] as const,
			queryFn: async ({ signal }) => await fetchReport(reportId, signal),
		}),
	// GOOD: the request reads the ID from the key it is cached under.
	detail: (reportId: string) =>
		queryOptions({
			queryKey: ['reports', 'detail', reportId] as const,
			queryFn: async ({ queryKey: [, , keyReportId], signal }) =>
				await fetchReport(keyReportId, signal),
		}),
};
```

Reading from the context makes request identity visible at the I/O boundary: the request is built from exactly the values that identify its cache entry.

Avoid captures where possible, but they are not banned. A captured value that does not change the response, such as an API client or a stable helper function, stays in the closure. Every captured value that changes the response must be in the key; otherwise requests with different responses share one cache entry. Once it is in the key, read it from there.

Do not mutate key values or React state from `queryFn`. Never put secrets, functions, clients, or `AbortSignal` instances in a key; use a non-secret actor or session partition when visibility changes the response.

Do not hide response-changing arguments in `meta`, mutable module state, ambient configuration, or an imported helper's closure. Keep presentation-only values outside the query function and key.
