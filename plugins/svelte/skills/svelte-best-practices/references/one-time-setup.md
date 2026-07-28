# One-Time Setup

Use an attachment for one-time client lifecycle work that belongs to an element. Use `onMount` only when component lifecycle work has no element owner. Do not use `$effect` when the work is not reactive.

```svelte
<!-- BAD: Treat Non-Reactive Setup as an Effect -->

<script lang="ts">
	let element = $state<HTMLElement | undefined>();

	$effect(() => {
		if (typeof element !== 'undefined')
			initThirdPartyLib(element);
	});
</script>
```

```svelte
<!-- GOOD: Let the Attached Element Own Setup and Cleanup -->

<script lang="ts">
	function setupThirdParty(element: HTMLElement) {
		const controller = new AbortController();
		initThirdPartyLib(element, { signal: controller.signal });
		return () => controller.abort();
	}
</script>

<div {@attach setupThirdParty}></div>
```
