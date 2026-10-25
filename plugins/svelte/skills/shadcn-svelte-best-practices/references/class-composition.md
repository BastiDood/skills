# Class Composition

Write static class lists as literal strings. Use `cn` when composition or merging is needed: object syntax for independent conditions, a ternary for mutually exclusive alternatives, and one final class value. Let `cn` compose the list instead of prebuilding it with interpolation, concatenation, or mutable `className` assembly.

```svelte
<!-- BAD: unnecessary composition or ad hoc conditional lists. -->
<div class={cn('flex items-center')}></div>
<div class={cn(`flex items-center ${isDisabled ? 'cursor-not-allowed opacity-50' : ''}`)}></div>
<div class={cn('flex items-center', isDisabled && 'cursor-not-allowed opacity-50')}></div>

<!-- GOOD: literal static classes. -->
<div class="flex items-center"></div>

<!-- GOOD: independent classes use object syntax. -->
<div
	class={cn('flex items-center', {
		'cursor-not-allowed': isDisabled,
		'opacity-50': isDisabled,
	})}
></div>

<!-- GOOD: mutually exclusive classes use a ternary. -->
<div
	class={cn(
		'flex items-center',
		isSelected ? 'bg-primary text-primary-foreground' : 'bg-background text-foreground',
	)}
></div>

<!-- GOOD: wrappers still merge defaults with caller classes. -->
<div class={cn('flex items-center p-6', className)}></div>
```

Preserve the project's design tokens and semantic controls. A spacing correction should change only the relevant spacing utility; retain unrelated padding, container-query ownership, and intentional caller overrides.
