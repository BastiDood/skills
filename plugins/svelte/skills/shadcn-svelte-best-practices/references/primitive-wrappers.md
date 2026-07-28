# Primitive Wrappers

Every wrapper around a Bits UI primitive exposes `ref = $bindable(null)`, merges `class` through `cn(...)`, and forwards `...restProps`. This keeps wrappers drop-in compatible with the primitive's full API instead of allowlisting props one by one.

```svelte
<!-- BAD: Narrow the Primitive Surface -->

<script lang="ts">
	import { Tooltip as TooltipPrimitive } from 'bits-ui';
	import type { Snippet } from 'svelte';

	interface Props {
		children?: Snippet;
	}

	let { children }: Props = $props();
</script>

<TooltipPrimitive.Content>{@render children?.()}</TooltipPrimitive.Content>
```

```svelte
<!-- GOOD: Preserve the Primitive Surface -->

<script lang="ts">
	import { Tooltip as TooltipPrimitive } from 'bits-ui';
	import type { ComponentProps } from 'svelte';

	import { cn } from '$lib/utils';

	let {
		ref = $bindable(null),
		class: className,
		sideOffset = 4,
		children,
		...restProps
	} = $props<ComponentProps<typeof TooltipPrimitive.Content>>();
</script>

<TooltipPrimitive.Content bind:ref {sideOffset} class={cn('tooltip-content', className)} {...restProps}>
	{@render children?.()}
</TooltipPrimitive.Content>
```
