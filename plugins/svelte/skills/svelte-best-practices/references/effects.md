# Effects

Default to no effect. Treat `$effect` as an escape hatch used only to synchronize Svelte with an external system that Svelte does not control.

- An effect is data-reactive: Svelte tracks values read by the effect and reruns it when those values change.
- An event handler is event-based: it runs because a discrete event occurred. Do not route an event through state so an effect can react to it.

Do not use `$effect` for derivation, user actions, data fetching, state reset, or ordered application work. Before adding one, identify the external system and the reactive value that must remain synchronized with it.

```svelte
<!-- BAD: turns a save event into a reactive signal. -->
<script lang="ts">
	interface Props {
		save: () => void;
	}

	const { save }: Props = $props();
	let shouldSave = $state(false);

	$effect(() => {
		if (shouldSave) save();
	});
</script>

<button type="button" onclick={() => shouldSave = true}>Save</button>
```

```svelte
<!-- GOOD: a reactive subscription owns its external connection and cleanup. -->
<script lang="ts">
	interface RoomConnection {
		disconnect: () => void;
	}

	interface Props {
		roomId: string;
		connectToRoom: (roomId: string) => RoomConnection;
	}

	const { roomId, connectToRoom }: Props = $props();

	$effect(() => {
		const connection = connectToRoom(roomId);
		return () => connection.disconnect();
	});
</script>
```

Changing rooms disconnects the previous subscription; unmounting releases the current one. Keep ordinary route data in its existing loader or query owner. Render document metadata declaratively so it also participates in server rendering:

```svelte
<script lang="ts">
	interface Props {
		title: string;
	}

	const { title }: Props = $props();
</script>

<svelte:head>
	<title>{title}</title>
</svelte:head>
```
