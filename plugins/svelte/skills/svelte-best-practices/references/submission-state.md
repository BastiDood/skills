# Submission State

Model pending work as form-wide state. Disable every submit control while submission is active, and clear pending state when the operation settles.

```svelte
<!-- BAD: one button does not model the lifetime of the form operation. -->
<button type="submit" disabled={isSubmitting && activeAction === 'create'}>Create</button>

<!-- GOOD: all submit actions reflect form-wide pending work. -->
<button type="submit" disabled={isSubmitting}>Create</button>
<button type="submit" disabled={isSubmitting}>Save Draft</button>

{#if isSubmitting}
	<p role="status">Saving changes...</p>
{/if}
```

Keep validation and network errors distinct from pending state so a failed submission leaves controls usable. Render cancellation, validation, and network failures explicitly.

Do not use a disabled button as the only indication of progress. Announce noticeable work with accessible status text that does not depend on which submit control initiated it.
