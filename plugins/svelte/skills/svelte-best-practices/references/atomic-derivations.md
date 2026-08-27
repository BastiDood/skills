# Atomic Derivations

Cache every pure computation of reactive inputs worse than O(1) with `$derived`, including O(log n) searches and O(n) scans such as `some`, `find`, `includes`, or `reduce`. This applies to scalar results too. Keep independent calculations separate so unrelated inputs do not invalidate them together.

In this component excerpt, `items` and `descending` are reactive inputs.

```svelte
<script lang="ts">
	// BAD: changing descending also recomputes total.
	const computed = $derived({
		sorted: items.toSorted((a, b) =>
			descending ? b.name.localeCompare(a.name) : a.name.localeCompare(b.name),
		),
		total: items.reduce((sum, item) => sum + item.amount, 0),
	});

	// GOOD: changing descending leaves total cached.
	const sortedItems = $derived(
		items.toSorted((a, b) =>
			descending ? b.name.localeCompare(a.name) : a.name.localeCompare(b.name),
		),
	);
	const total = $derived(items.reduce((sum, item) => sum + item.amount, 0));
</script>
```

O(1) reactive values still need `$derived` when named in a script: `const doubled = $derived(count * 2)` stays current; `const doubled = count * 2` is an initial snapshot. Keep derivations pure; do not write state or perform I/O inside them.
