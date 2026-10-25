# Submission State

Read pending, result, and failure state from the existing submission owner. Pass that state down when the owner lives above the controls; do not mirror it in another local flag or couple it to bound input state. Every submit control reflects the same operation lifetime.

```svelte
<script lang="ts">
	interface Props {
		pending: boolean;
		error: Error | null;
	}

	const { pending, error }: Props = $props();
</script>

<!-- The parent form supplies its submission owner's state to these controls. -->
<button type="submit" disabled={pending}>Create</button>
<button type="submit" disabled={pending}>Save Draft</button>

{#if pending}
	<p role="status">Saving changes...</p>
{/if}

{#if error !== null}
	<p role="alert">{error.message}</p>
{/if}
```

Keep validation, cancellation, and request failures distinct from progress so controls remain usable after the operation settles. Announce noticeable work with accessible status text that does not depend on which submit control initiated it. Choose draft reset behavior deliberately after the intended outcome; input edits and submission status have separate lifetimes.
