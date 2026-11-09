# Reactive Query Options

Pass an accessor to `createQuery`. The Svelte adapter tracks reactive values read by that accessor and updates the query when its options change. With query factories, the accessor reads the current inputs and calls the factory.

```svelte
<script lang="ts">
	import { createQuery } from '@tanstack/svelte-query';

	import { itemQueries } from './item-queries';

	interface Props {
		categoryId: string;
	}

	const { categoryId }: Props = $props();

	// BAD: the factory runs once and freezes the options at the initial category.
	const options = itemQueries.list(categoryId);
	const stale = createQuery(() => options);

	// GOOD: the accessor reads the prop each time it runs.
	const query = createQuery(() => itemQueries.list(categoryId));
</script>
```

The Svelte compiler warns about the "BAD" form with `state_referenced_locally`. `createQuery` takes its options as an accessor for this reason. Keep query factories free of runes so they stay plain functions; cache an expensive non-constant derivation with `$derived` in the component, then read it in the accessor.
