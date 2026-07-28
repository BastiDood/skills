# Absent Identifiers

Disable a query when a required identifier is absent, and preserve that absence in the key.

```svelte
<script lang="ts">
	import { skipToken } from '@tanstack/svelte-query';

	// BAD: absence becomes a request for a fabricated record.
	const invalidKey = jobQueryKeys.detail(jobId ?? '');

	// GOOD: preserve absence and do not execute a request.
	const query = createQuery(() => ({
		queryKey: jobQueryKeys.detail(jobId),
		queryFn: typeof jobId === 'undefined' ? skipToken : async () => await fetchJob(jobId),
	}));
</script>
```

Do not replace absence with `''`, `0`, a sentinel UUID, or another fabricated request value. Model `null` separately when the domain distinguishes it from `undefined`.
