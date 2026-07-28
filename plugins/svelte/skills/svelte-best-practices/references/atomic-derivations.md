# Atomic Derivations

`$derived` is memoized. Keep unrelated calculations in separate derived values so a change only recomputes the calculation that depends on it.

```svelte
<script lang="ts">
	// BAD: both calculations recompute together
	const computed = $derived({
		sorted: items.toSorted((a, b) => a.name.localeCompare(b.name)),
		total: items.reduce((sum, item) => sum + item.amount, 0),
	});

	// GOOD: each result has its own dependency surface.
	const sortedItems = $derived(items.toSorted((a, b) => a.name.localeCompare(b.name)));
	const total = $derived(items.reduce((sum, item) => sum + item.amount, 0));
</script>
```
