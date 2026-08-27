# Reactive Query Options

Pass an accessor to `createQuery`. The Svelte adapter tracks reactive values read by that accessor and updates the query when its options change. A reusable query abstraction receives reactive inputs as getters, then reads them inside that accessor.

```svelte
<script lang="ts">
	const { enabled }: Props = $props();

	// BAD: plain options do not expose reactive reads to the adapter
	const stale = createQuery({
		queryKey: ['items'],
		queryFn: async () => await fetchItems(),
		enabled,
	});

	// GOOD: accessor-based reactive options
	const query = createQuery(() => ({
		queryKey: ['items'],
		queryFn: async () => await fetchItems(),
		enabled,
	}));
</script>
```

Keep runes in a `.svelte.ts` module when reusable query logic uses them. Cache non-constant derivation with `$derived` inside the feature abstraction, then read it in the accessor. Do not use React `useMemo`.
