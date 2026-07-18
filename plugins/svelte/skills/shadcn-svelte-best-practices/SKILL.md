---
name: shadcn-svelte-best-practices
description: Opinionated shadcn-svelte and Bits UI conventions for wrapping primitives, variants, and styling hooks. Use when adding or restyling UI components in Svelte.
---

# shadcn-svelte Best Practices

Opinionated conventions for building on `shadcn-svelte` and `bits-ui`. API documentation lives elsewhere (see the footer); every section here is a rule.

## Wrap Primitives with a Bindable Ref and Rest Props

Every wrapper around a Bits UI primitive exposes `ref = $bindable(null)`, merges `class` through `cn(...)`, and forwards `...restProps`. This keeps wrappers drop-in compatible with the primitive's full API instead of allowlisting props one by one.

```svelte
<script lang="ts">
	import { Tooltip as TooltipPrimitive } from 'bits-ui';

	let {
		ref = $bindable(null),
		class: className,
		sideOffset = 4,
		children,
		...restProps
	} = $props();
</script>

<TooltipPrimitive.Content bind:ref {sideOffset} class={cn('...', className)} {...restProps}>
	{@render children?.()}
</TooltipPrimitive.Content>
```

## Define Variants in Module Script

`tv()` variant definitions live in `<script lang="ts" module>`, not the instance script. Module scope makes the variants and their `VariantProps` type importable by consumers without instantiating the component.

```svelte
<script lang="ts" module>
	import { tv, type VariantProps } from 'tailwind-variants';

	export const buttonVariants = tv({
		base: 'inline-flex items-center justify-center rounded-md text-sm font-medium',
		variants: {
			variant: {
				default: 'bg-primary text-primary-foreground hover:bg-primary/90',
				destructive: 'bg-destructive text-destructive-foreground',
				outline: 'border bg-background hover:bg-accent',
			},
			size: { default: 'h-10 px-4 py-2', sm: 'h-9 px-3', lg: 'h-11 px-8', icon: 'size-10' },
		},
		defaultVariants: { variant: 'default', size: 'default' },
	});

	export type ButtonVariant = VariantProps<typeof buttonVariants>['variant'];
</script>

<button class={cn(buttonVariants({ variant, size }), className)}>
	{@render children?.()}
</button>
```

## Mark Wrapper Elements with `data-slot`

Add a `data-slot` attribute to each wrapper element. It gives consumers a stable external styling hook (`[data-slot='card-header']`) that survives internal class-list churn.

```svelte
<div data-slot="card-header" class={cn('flex flex-col gap-1.5 p-6', className)} {...restProps}>
	{@render children?.()}
</div>
```

## Documentation

- If available, use Context7 (Library ID: `/huntabyte/bits-ui`) to fetch `bits-ui` documentation.
- If available, use Context7 (Library ID: `/huntabyte/shadcn-svelte`) to fetch `shadcn-svelte` documentation.
- If available, use DeepWiki (GitHub Repository: `huntabyte/bits-ui`) for `bits-ui` implementation details.
