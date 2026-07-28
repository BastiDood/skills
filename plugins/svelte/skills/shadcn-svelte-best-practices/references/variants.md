# Variants

Put `tv()` variant definitions in `<script lang="ts" module>`, not the instance script. Module scope makes variants and their `VariantProps` type importable without instantiating the component.

```svelte
<!-- BAD: Define Shared Variant Schema per Instance -->

<script lang="ts">
	import { tv } from 'tailwind-variants';

	const variants = tv({ variants: { size: { sm: 'h-8' } } });
</script>
```

```svelte
<!-- GOOD: Export Stable Variant Schema From Module Scope -->

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
	export type ButtonSize = VariantProps<typeof buttonVariants>['size'];
</script>

<script lang="ts">
	import type { Snippet } from 'svelte';

	import { cn } from '$lib/utils';

	interface Props {
		variant?: ButtonVariant;
		size?: ButtonSize;
		class?: string;
		children?: Snippet;
	}

	let { variant, size, class: className, children }: Props = $props();
</script>

<button class={cn(buttonVariants({ variant, size }), className)}>
	{@render children?.()}
</button>
```
