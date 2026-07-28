# Class Composition

Use `cn` for conditional class composition. Do not interpolate class strings or pass `condition && 'class'` to `cn`. Use object syntax for independently toggled classes and a ternary for mutually exclusive classes.

```svelte
<!-- BAD: implicit class composition -->
<div class={`flex items-center ${isDisabled ? 'cursor-not-allowed opacity-50' : ''}`}></div>
<div class={cn('flex items-center', isDisabled && 'cursor-not-allowed opacity-50')}></div>

<!-- GOOD: independent classes use object syntax -->
<div
	class={cn('flex items-center', {
		'cursor-not-allowed': isDisabled,
		'opacity-50': isDisabled,
	})}
></div>

<!-- GOOD: mutually exclusive classes use a ternary -->
<div
	class={cn(
		'flex items-center',
		isSelected ? 'bg-primary text-primary-foreground' : 'bg-background text-foreground',
	)}
></div>
```
