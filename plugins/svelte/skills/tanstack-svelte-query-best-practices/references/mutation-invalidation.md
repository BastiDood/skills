# Mutation Invalidation

Invalidate the cache entry identified by successful mutation variables. Use the callback context's `QueryClient`.

```typescript
// BAD: every successful edit invalidates unrelated records.
queryClient.invalidateQueries({ queryKey: jobQueryKeys.all });

// GOOD: mutation variables identify the exact stale cache entry.
const mutation = createMutation(() => ({
	mutationFn: updateJob,
	onSuccess(_data, variables, _onMutateResult, context) {
		context.client.invalidateQueries({ queryKey: jobQueryKeys.detail(variables.id) });
	},
}));
```

Invalidate the narrowest query family that became stale. Invalidate a list only when the mutation changes list membership, ordering, or displayed summary data.

Do not acquire a client from component scope inside the callback. Keep optimistic-update sequencing in the owning project when its policy does not generalize.
