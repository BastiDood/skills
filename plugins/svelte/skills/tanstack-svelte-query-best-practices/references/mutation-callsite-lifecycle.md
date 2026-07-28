# Mutation Call-Site Lifecycle

Use per-call callbacks for operation outcomes. `onSuccess` owns success-only consequences, `onError` owns expected failure presentation, and `onSettled` clears state required after either outcome.

```typescript
// BAD: a reactive effect loses the identity of the invocation that succeeded.
$effect(() => {
	if (mutation.isSuccess) closeDialog();
});

// GOOD: mounted UI consequences stay at the call site.
mutation.mutate(parsed.output, {
	onSuccess: () => closeDialog(),
	onError: error => {
		formError = toExpectedMessage(error);
	},
	onSettled: () => {
		isSubmitting = false;
	},
});
```

Do not use an effect to react to `mutation.isSuccess`. Keep the consequence attached to the invocation that caused it.

Use `mutateAsync` only when the caller genuinely composes the returned promise into a larger asynchronous operation. Do not add ceremonial `try`/`finally` merely to clear pending state.

Per-call callbacks do not run after their observer unmounts, and consecutive `mutate` calls guarantee them only for the latest observer. Put consequences that must run for every invocation in the mutation definition instead.
