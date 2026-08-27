# Complex Derivations

Use `$derived(expression)` for an expression and `$derived.by(() => { ... })` for multiple statements. Both cache their result; choose by syntax, not complexity. Keep the calculation pure: read reactive inputs and return a value without changing them or doing I/O.

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
