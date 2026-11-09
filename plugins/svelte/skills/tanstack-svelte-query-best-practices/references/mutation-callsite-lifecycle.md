# Mutation Call-Site Lifecycle

Attach mounted UI consequences to the invocation that caused them. Read pending and failure state from the mutation or the chosen form owner, rather than maintaining another submission flag.

```svelte
<script lang="ts">
	import { createMutation } from '@tanstack/svelte-query';

	interface Item {
		id: string;
		name: string;
	}

	interface Props {
		updateItem: (input: Item) => Promise<Item>;
		closeDialog: () => void;
	}

	const { updateItem, closeDialog }: Props = $props();
	const mutation = createMutation(() => ({ mutationFn: updateItem }));

	// BAD: a reactive effect loses the identity of the successful invocation.
	$effect(() => {
		if (mutation.isSuccess) closeDialog();
	});

	// GOOD: this invocation owns its success consequence.
	function submit(input: Item) {
		mutation.mutate(input, { onSuccess: () => closeDialog() });
	}
</script>
```

Use per-call `onError` for invocation-specific expected-failure presentation and `onSettled` for real cleanup needed after either result. Do not use settlement callbacks merely to clear a duplicate pending flag.

Per-call callbacks do not run if the component unmounts before the mutation finishes. Consecutive `mutate` calls fire them only once, for the last call. Required every-invocation policy belongs in the definition. Definition callbacks run for every invocation.

## Concurrent Invocations

Use `mutateAsync` only inside a `Promise.*` combinator, when one mutation runs concurrently for several inputs and the caller awaits them together. Only the last call's callbacks fire. Per-call callbacks cannot express that consequence.

```svelte
<script lang="ts">
	import { createMutation } from '@tanstack/svelte-query';

	interface Props {
		jobIds: readonly string[];
		archiveJob: (id: string) => Promise<void>;
		reportArchiveFailures: (count: number) => void;
		closePanel: () => void;
	}

	const { jobIds, archiveJob, reportArchiveFailures, closePanel }: Props = $props();
	const mutation = createMutation(() => ({ mutationFn: archiveJob }));

	// BAD: only the last invocation's callback runs, whichever request settles first.
	function archiveEach() {
		for (const id of jobIds) mutation.mutate(id, { onSuccess: () => closePanel() });
	}

	// GOOD: the combinator awaits every invocation before the shared consequence.
	async function archiveAll() {
		const results = await Promise.allSettled(jobIds.map(async id => await mutation.mutateAsync(id)));
		const { length: failureCount } = results.filter(result => result.status === 'rejected');
		if (failureCount > 0) reportArchiveFailures(failureCount);
		else closePanel();
	}
</script>

<button type="button" onclick={archiveEach}>Archive each</button>
<button type="button" onclick={archiveAll}>Archive selected</button>
```

Run a dependent sequence, where one request needs another's result, inside a single `mutationFn` instead of chaining `mutateAsync` calls. The mutation then has one pending state, one error, and one set of callbacks. Use `mutate` for a single invocation, including a single form submission.
