# Effects

Default to no effect. Treat `$effect` as an escape hatch used only to synchronize Svelte with an external system that Svelte does not control.

- An effect is data-reactive: Svelte tracks values read by the effect and reruns it when those values change.
- An event handler is event-based: it runs because a discrete event occurred. Do not call event handling effect handling, and do not route an event through state so an effect can react to it.

Do not use `$effect` for derivation, user actions, data fetching, state reset, or ordered application work. Before adding one, identify the external system and the reactive value that must remain synchronized with it. If neither exists, do not add the effect.

```svelte
<!-- BAD: Turn an Event Into a Reactive Signal -->

<script lang="ts">
	let shouldSave = $state(false);

	$effect(() => {
		if (shouldSave) void saveDraft();
	});
</script>
```

```svelte
<!-- GOOD: Synchronize an External Resource -->

<script lang="ts">
	let title = $state('Dashboard');

	$effect(() => {
		const previousTitle = document.title;
		document.title = title;
		return () => {
			document.title = previousTitle;
		};
	});
</script>
```
