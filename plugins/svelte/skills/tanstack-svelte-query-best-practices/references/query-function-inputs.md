# Query Function Inputs

Keep request construction visibly aligned with cache identity. A closure is valid when every captured response-changing value is represented in the query key.

Declare every `queryFn` as `async` and `await` its request. Do not return a bare promise expression.

```svelte
<script lang="ts">
	// BAD: locale changes the request but is absent from cache identity.
	const staleQuery = createQuery(() => ({
		queryKey: ['items'] as const,
		queryFn: async () => await fetchItems(locale),
	}));

	// GOOD: the request and key expose the same response-changing input.
	const query = createQuery(() => ({
		queryKey: itemQueryKeys.detail(itemId),
		queryFn: async () => await fetchItem(itemId),
	}));
</script>
```

Derive inputs from the query-function context when that makes the correspondence clearer or lets one function serve several callers.

Do not claim closures are inherently stale. The defect is a hidden request input that does not participate in cache identity. Do not capture mutable globals, timestamps, or other ambient values that change a request without changing its key.
