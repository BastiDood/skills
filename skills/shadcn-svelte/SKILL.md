---
name: shadcn-svelte
description: UI components with `shadcn-svelte` and `bits-ui`. Use when adding UI components, styling, buttons, dialogs, drawers, forms, or any user interface elements.
---

# `shadcn-svelte` + `bits-ui` UI Components

Styled UI components built on headless primitives.

**Libraries:** `bits-ui`, `shadcn-svelte`, `vaul-svelte`, `tailwind-variants`, `@lucide/svelte`

## Documentation

- If available, use Context7 (Library ID: `/huntabyte/bits-ui`) to fetch `bits-ui` documentation.
- If available, use Context7 (Library ID: `/huntabyte/shadcn-svelte`) to fetch `shadcn-svelte` documentation.
- If available, use DeepWiki (GitHub Repository: `huntabyte/bits-ui`) for `bits-ui` implementation details.

## Adding Components

```bash
pnpm dlx shadcn-svelte add <component-name>
```

## Using Components

```svelte
<script lang="ts">
	import * as Drawer from '$lib/components/ui/drawer';
	import * as Select from '$lib/components/ui/select';
	import * as Dialog from '$lib/components/ui/dialog';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';
</script>

<Button variant="outline" size="sm" onclick={handleClick}>Click me</Button>

<Dialog.Root bind:open>
	<Dialog.Content>
		<Dialog.Header>
			<Dialog.Title>Settings</Dialog.Title>
			<Dialog.Description>Configure your preferences.</Dialog.Description>
		</Dialog.Header>
		<!-- Content here -->
	</Dialog.Content>
</Dialog.Root>
```

## The `cn(...)` Utility

```typescript
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
	return twMerge(clsx(inputs));
}
```

## tailwind-variants for Variants

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
</script>

<button class={cn(buttonVariants({ variant, size }), className)}>
	{@render children?.()}
</button>
```

## Wrapping `bits-ui` Primitives

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

## Icons with Lucide

```svelte
<script lang="ts">
	import MapPinIcon from '@lucide/svelte/icons/map-pin';
	import LoaderCircle from '@lucide/svelte/icons/loader-circle';
</script>

<Button variant="outline" size="icon"><MapPinIcon /></Button>
<LoaderCircle class="size-4 animate-spin" />
```

## Best Practices

1. **Use namespace imports** for compound components: `import * as Drawer from '...'`
2. **Use direct imports** for simple components: `import { Button } from '...'`
3. **Always use `cn()`** for class merging
4. **Use `$bindable()`** for two-way binding props (open, value, checked)
5. **Define variants in module script** for reuse and type export
6. **Add `data-slot` attributes** for external styling hooks
