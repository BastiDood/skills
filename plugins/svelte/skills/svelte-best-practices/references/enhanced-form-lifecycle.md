# Enhanced Form Lifecycle

Use `use:enhance` without a callback when its default reset, invalidation, and action-result behavior is sufficient. A custom submit callback replaces that behavior, so call `update()` deliberately.

```svelte
<!-- BAD: a custom callback replaces SvelteKit defaults and never restores them. -->
<form method="POST" use:enhance={() => () => { isSubmitting = false; }}></form>

<!-- GOOD: call update() deliberately after custom lifecycle work. -->
<form
	method="POST"
	use:enhance={() => {
		isSubmitting = true;
		return async ({ result, update }) => {
			isSubmitting = false;
			await update({ reset: true, invalidateAll: true });
			if (result.type === 'success') onSuccess();
		};
	}}
></form>
```

For a dialog or sheet, close it only after a successful result. Keep it open for validation or server failures so the user can correct the submission.
