# Complex Derivations

Use `$derived.by()` when a derivation needs multi-step, `O(n)` or worse work. Keep the calculation pure: it reads reactive state and returns a value without writing state or doing I/O.

```svelte
<!-- BAD: Synchronize a Derived Value Through an Effect -->

<script lang="ts">
	import { SvelteMap } from 'svelte/reactivity';

	interface Item {
		category: string;
	}

	function groupItems(items: Item[]) {
		const groups = new SvelteMap<string, Item[]>();
		for (const item of items) {
			const group = groups.get(item.category) ?? [];
			group.push(item);
			groups.set(item.category, group);
		}
		return groups;
	}

	let items = $state<Item[]>([]);
	let grouped = $state(groupItems(items));

	$effect(() => {
		grouped = groupItems(items);
	});
</script>
```

```svelte
<!-- GOOD: Derive the Grouping Directly -->

<script lang="ts">
	interface Item {
		category: string;
	}

	let items = $state<Item[]>([]);

	const grouped = $derived.by(() => {
		const groups = new Map<string, Item[]>();
		for (const item of items) {
			const group = groups.get(item.category) ?? [];
			group.push(item);
			groups.set(item.category, group);
		}
		return groups;
	});
</script>
```
