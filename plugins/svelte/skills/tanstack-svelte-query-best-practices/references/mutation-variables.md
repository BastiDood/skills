# Mutation Variables

Pass changing operation input through `mutate(...)` variables. Keep only stable mutation configuration in the mutation definition closure.

```typescript
// BAD: changing input is hidden in mutation construction.
const mutation = createMutation(() => ({ mutationFn: data => updateJob(jobId, data) }));

// GOOD: every mutation call supplies its own affected record.
interface UpdateJobVariables {
	id: string;
	data: UpdateJobRequest;
}

const mutation = createMutation(() => ({
	async mutationFn({ id, data }: UpdateJobVariables) {
		return await updateJob(id, data);
	},
}));
```

This makes each request explicit and lets callbacks identify the affected record through `variables`. Do not create one mutation instance per changing identifier or hide an identifier in component state for the mutation function to read later.
