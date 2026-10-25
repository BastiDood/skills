# Mutation Callback Ownership

Put every-invocation cache policy in the mutation definition and mounted component consequences at the call site. Use returned data for the confirmed outcome and variables for submitted identity, rather than reconstructing either from changing component state.

```svelte
<script lang="ts">
	import { createMutation } from '@tanstack/svelte-query';

	interface Item {
		id: string;
		name: string;
	}

	interface Props {
		draftName: string;
		updateItem: (input: Item) => Promise<Item>;
		closeDialog: () => void;
		showSavedName: (name: string) => void;
	}

	const { draftName, updateItem, closeDialog, showSavedName }: Props = $props();
	const itemQueryKeys = {
		detail(id: string) {
			return ['items', 'detail', id] as const;
		},
	};

	// BAD: shared cache work depends on an observer, and a later draft is not the result.
	const badMutation = createMutation(() => ({
		mutationFn: updateItem,
		onSuccess: () => closeDialog(),
	}));

	function submitBad(variables: Item) {
		badMutation.mutate(variables, {
			async onSuccess(_data, submittedVariables, _onMutateResult, context) {
				await context.client.invalidateQueries({ queryKey: itemQueryKeys.detail(submittedVariables.id) });
				showSavedName(draftName);
			},
		});
	}

	// GOOD: each invocation refreshes its entry; mounted UI uses its confirmed result.
	const mutation = createMutation(() => ({
		mutationFn: updateItem,
		async onSuccess(_data, variables, _onMutateResult, context) {
			await context.client.invalidateQueries({ queryKey: itemQueryKeys.detail(variables.id) });
		},
	}));

	function submit(variables: Item) {
		mutation.mutate(variables, {
			onSuccess(savedItem) {
				showSavedName(savedItem.name);
				closeDialog();
			},
		});
	}
</script>
```

Per-call callbacks may be skipped after unmount or superseded observers, so they cannot own consequences required for every invocation. Capturing a stable UI capability such as `closeDialog` remains appropriate. Read pending and failure state from the mutation rather than maintaining another submission flag.
