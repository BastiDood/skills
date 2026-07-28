# State Reset

Remount a stateful component with `{#key}` when an identity change requires fresh local state. Do not use `$effect` to reset each field manually.

```svelte
<!-- BAD: an effect manually reconstructs a state lifetime. -->
<script lang="ts">
	$effect(() => {
		if (typeof categoryId !== 'undefined') selectedIds = new Set();
	});
</script>

<!-- GOOD: a category change recreates ItemList and its local state -->
{#key categoryId}
	<ItemList {items} />
{/key}
```

Use `{#key}` only when a changed domain identity starts a new local-state lifetime. Do not use an arbitrary counter or random key as a force-refresh.

If state must survive the identity change, preserve it in the actual owner instead. Keep the keyed boundary narrow so unrelated sibling state does not reset with it.
