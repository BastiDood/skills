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

Use `mutateAsync` when the client submit driver or another caller awaits mutation completion as part of a larger operation. Do not add ceremonial `try`/`finally` to recreate mutation lifecycle state. Per-call callbacks stop after observer unmount and consecutive calls retain only the latest observer's callbacks; required every-invocation policy belongs in the definition.
