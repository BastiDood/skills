# Effect Chains

Do not coordinate an ordered operation by chaining `$effect` blocks through intermediate state. The order becomes implicit, partial failure is difficult to represent, and each effect can rerun independently.

```svelte
<!-- BAD: Hide an Ordered Operation in Reactive Stages -->

<script lang="ts">
	let first = $state<string | undefined>();
	let second = $state<string | undefined>();
	let third = $state<string | undefined>();

	$effect(() => {
		void fetchFirst().then(value => {
			first = value;
		});
	});

	$effect(() => {
		if (typeof first !== 'undefined') {
			void fetchSecond(first).then(value => {
				second = value;
			});
		}
	});

	$effect(() => {
		if (typeof second !== 'undefined') {
			void fetchThird(second).then(value => {
				third = value;
			});
		}
	});
</script>
```

```svelte
<!-- GOOD: Keep the Order in One Event-Owned Operation -->

<script lang="ts">
	interface LoadedState {
		first: string;
		second: string;
		third: string;
	}

	let state = $state<LoadedState | undefined>();

	async function handleLoad() {
		const first = await fetchFirst();
		const second = await fetchSecond(first);
		const third = await fetchThird(second);
		state = { first, second, third };
	}
</script>
```
