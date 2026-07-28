# Mutation Form Integration

Use a SvelteKit action with progressive enhancement when the application has a form-action lifecycle.

```svelte
<script lang="ts">
	import { enhance } from '$app/forms';
</script>

<!-- BAD: one form owns two competing submission lifecycles. -->
<form method="POST" use:enhance onsubmit={handleMutationSubmit}></form>

<!-- GOOD: choose SvelteKit action ownership for this form. -->
<form method="POST" use:enhance>
	<!-- fields and submit controls -->
</form>
```

Use an `onsubmit` handler with a TanStack mutation only in SPA mode, for an external API, or where no SvelteKit action exists.

```svelte
<script lang="ts">
	interface Props {
		handleMutationSubmit(event: SubmitEvent): void;
	}

	const { handleMutationSubmit }: Props = $props();
</script>

<form onsubmit={handleMutationSubmit}>
	<!-- fields and submit controls -->
</form>
```

Do not combine both submission lifecycles for one form.
