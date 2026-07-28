# Snippets

Use a plain `Snippet` for passive content that the component places in its layout.

```svelte
<!-- BAD: a legacy slot hides the component's composition contract. -->
<section>
	<slot />
</section>
```

```svelte
<script lang="ts">
	import type { Snippet } from 'svelte';

	// GOOD: the prop states the passive composition contract.
	interface Props {
		children: Snippet;
	}

	let { children }: Props = $props();
</script>

<section>
	{@render children()}
</section>
```

Use a typed snippet parameter only when the component provides behavior or state to the child.

```svelte
<script lang="ts">
	import type { Snippet } from 'svelte';

	// GOOD: the parameter is justified because the component provides behavior.
	interface ToggleState {
		isOpen: boolean;
		toggle(): void;
	}

	interface Props {
		children: Snippet<[ToggleState]>;
	}

	let { children }: Props = $props();
	let isOpen = $state(false);
	function toggle() {
		isOpen = !isOpen;
	}
</script>

{@render children({ isOpen, toggle })}
```

Do not parameterize a snippet for static markup. Do not expose both passive and behavioral children for the same composition point unless they are genuinely distinct modes.

Require a child snippet when the component is meaningless without content. Mark it optional and render it with `children?.()` only when empty composition is valid. Do not use legacy `<slot>` elements.
