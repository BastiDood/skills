# Absent Identifiers

Disable a query when a required identifier is absent, and preserve that absence in the key.

Component excerpt: `jobId` is `string | undefined`; `jobQueryKeys.detail(id)` returns `['jobs', 'detail', id] as const`, preserving that type. `fetchJob(id, signal)` requires a string ID. Prop declarations and other imports are omitted.

```svelte
<script lang="ts">
	import { skipToken } from '@tanstack/svelte-query';

	// BAD: absence becomes a request for a fabricated record.
	const invalidKey = jobQueryKeys.detail(jobId ?? '');

	// GOOD: preserve absence and do not execute a request.
	const query = createQuery(() => ({
		queryKey: jobQueryKeys.detail(jobId),
		queryFn:
			typeof jobId === 'undefined'
				? skipToken
				: async ({ queryKey: [, , keyJobId], signal }) => {
						if (typeof keyJobId === 'undefined') throw new Error('Expected a job ID');
						return await fetchJob(keyJobId, signal);
					},
	}));
</script>
```

Keep `createQuery` unconditional. Choosing `skipToken` from outer state does not narrow the optional ID in the query-function context, so guard the key-derived ID before the request. This avoids a capture, cast, non-null assertion, or fake default.

Do not replace absence with `''`, `0`, a sentinel UUID, or another fabricated request value. `skipToken` does not support manual refetch; model a manually triggered request separately. Model `null` separately when the domain distinguishes it from `undefined`.
