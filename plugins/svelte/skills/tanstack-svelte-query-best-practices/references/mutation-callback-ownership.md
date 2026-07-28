# Mutation Callback Ownership

Split callbacks by concern. Cache policy belongs to the mutation definition; component behavior belongs to the `mutate(...)` call site.

```typescript
// BAD: reusable cache policy is attached to one component invocation.
const badMutation = createMutation(() => ({
	mutationFn: updateItem,
	onSuccess: () => closeDialog(),
}));

badMutation.mutate(variables, {
	onSuccess: (_data, submittedVariables, _onMutateResult, context) => {
		context.client.invalidateQueries({
			queryKey: itemQueryKeys.detail(submittedVariables.id),
		});
	},
});
```

```typescript
// GOOD: shared cache policy and invocation-specific UI behavior have distinct owners.
const mutation = createMutation(() => ({
	mutationFn: updateItem,
	onSuccess(_data, variables, _onMutateResult, context) {
		context.client.invalidateQueries({ queryKey: itemQueryKeys.detail(variables.id) });
	},
}));

mutation.mutate(variables, {
	onSuccess: () => closeDialog(),
});
```

Definition callbacks run for every invocation and own shared cache consequences. Per-call callbacks own consequences specific to the invoking component.
