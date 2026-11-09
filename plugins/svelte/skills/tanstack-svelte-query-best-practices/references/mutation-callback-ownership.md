# Mutation Callback Ownership

Put every-invocation cache policy in the mutation definition and mounted component consequences at the call site. Use returned data for the confirmed outcome and variables for submitted identity, rather than reconstructing either from changing component state.

```svelte
<script lang="ts">
	import { createMutation } from '@tanstack/svelte-query';

	import { itemQueries } from './item-queries';

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

	// BAD: shared cache work depends on an observer. A later draft is not the result.
	const badMutation = createMutation(() => ({
		mutationFn: updateItem,
		onSuccess: () => closeDialog(),
	}));

	function submitBad(variables: Item) {
		badMutation.mutate(variables, {
			async onSuccess(_data, submittedVariables, _onMutateResult, context) {
				const { queryKey } = itemQueries.detail(submittedVariables.id);
				await context.client.invalidateQueries({ queryKey });
				showSavedName(draftName);
			},
		});
	}

	// GOOD: each invocation refreshes its entry. Mounted UI uses its confirmed result.
	const mutation = createMutation(() => ({
		mutationFn: updateItem,
		async onSuccess(_data, variables, _onMutateResult, context) {
			const { queryKey } = itemQueries.detail(variables.id);
			await context.client.invalidateQueries({ queryKey });
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

Per-call callbacks cannot own consequences required for every invocation. They do not run after unmount or for a superseded invocation. Capturing a stable UI capability such as `closeDialog` remains appropriate. Read pending and failure state from the mutation rather than maintaining another submission flag.
