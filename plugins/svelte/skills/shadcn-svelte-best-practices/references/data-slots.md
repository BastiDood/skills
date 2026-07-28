# Data Slots

Add a `data-slot` attribute to each wrapper element. It gives consumers a stable external styling hook, such as `[data-slot='card-header']`, that survives internal class-list churn.

```svelte
<!-- BAD: consumers must target volatile implementation classes. -->
<div class={cn('card-header', className)} {...restProps}>
	{@render children?.()}
</div>

<!-- GOOD: the stable slot names the wrapper's semantic role. -->
<div data-slot="card-header" class={cn('flex flex-col gap-1.5 p-6', className)} {...restProps}>
	{@render children?.()}
</div>
```
