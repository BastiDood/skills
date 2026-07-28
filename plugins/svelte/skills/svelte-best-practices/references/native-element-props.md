# Native Element Props

When a component wraps a native element, extend its attribute type, expose a bindable `ref`, and forward the remaining props. This preserves the native element API without allowlisting attributes one by one.

```svelte
<script lang="ts">
	import type { HTMLAttributes } from 'svelte/elements';

	// BAD: a handwritten surface excludes valid native attributes and bindings.
	interface IncompleteProps {
		class?: string;
	}

	// GOOD: inherit and forward the platform element contract.
	interface Props extends HTMLAttributes<HTMLDivElement> {
		ref?: HTMLDivElement | null;
	}

	let {
		ref = $bindable(null),
		class: className,
		children,
		...restProps
	}: Props = $props();
</script>

<div bind:this={ref} class={className} {...restProps}>
	{@render children?.()}
</div>
```
