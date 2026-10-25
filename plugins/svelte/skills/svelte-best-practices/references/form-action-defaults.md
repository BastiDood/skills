# Form Action Defaults

Keep named form fields unbound unless their live values drive rendered UI, such as validation feedback. Bound state serves that live presentation; submission reads `FormData` for both bound and unbound inputs rather than assembling a payload from the bound draft.

Use a SvelteKit form action with `method="POST"` and `use:enhance` by default. Use `onsubmit` with a client mutation only when no form-action lifecycle exists. Give each form one submission owner.

```svelte
<script lang="ts">
	import { enhance } from '$app/forms';

	interface Props {
		handleMutationSubmit: (event: SubmitEvent) => void;
	}

	const { handleMutationSubmit }: Props = $props();
</script>

<!-- BAD: competing owners submit the same operation. -->
<form method="POST" use:enhance onsubmit={handleMutationSubmit}></form>

<!-- GOOD: the page action owns progressive submission. -->
<form method="POST" use:enhance></form>
```

Use absolute `action` and `formaction` paths such as `/members?/promote`. Named actions still validate and authorize external request data on the server.
