# Mutation Form Integration

Use a SvelteKit action with progressive enhancement when the application has a form-action lifecycle.

```svelte
<script lang="ts">
	import { enhance } from '$app/forms';

	interface Props {
		handleMutationSubmit: (event: SubmitEvent) => void;
	}

	const { handleMutationSubmit }: Props = $props();
</script>

<!-- BAD: one form owns two competing submission lifecycles. -->
<form method="POST" use:enhance onsubmit={handleMutationSubmit}></form>

<!-- GOOD: this page action owns the form. -->
<form method="POST" use:enhance>
	<!-- fields and submit controls -->
</form>
```

For a client-owned API flow, use the existing mutation driver:

```svelte
<script lang="ts">
	interface Props {
		handleMutationSubmit: (event: SubmitEvent) => void;
	}

	const { handleMutationSubmit }: Props = $props();
</script>

<form onsubmit={handleMutationSubmit}>
	<!-- fields and submit controls -->
</form>
```

The submit driver prevents native submission, takes a fresh `FormData` snapshot, and decodes and validates it before calling the mutation. Bound input state supplies live rendered feedback, not the mutation payload. The mutation remains a minimal wrapper over the asynchronous boundary and owns its pending and failure state; do not duplicate that lifecycle in local flags.
