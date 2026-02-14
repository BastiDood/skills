# Side Effects in Svelte

## Core Principle

**`$effect` is almost never necessary.** 99% of side effects belong in event handlers, not in effects. Runes handle reactivity — effects are for external system sync only.

## Effects vs Events

|                  | Event Handlers                                    | `$effect`                                            |
| ---------------- | ------------------------------------------------- | ---------------------------------------------------- |
| **Triggered by** | User action (click, submit, navigate)             | Reactive dependency change                           |
| **When to use**  | Mutations, navigation, form submission, API calls | External system sync only                            |
| **Examples**     | `onclick`, `onsubmit`, `onchange`                 | DOM measurements, subscriptions, third-party widgets |

## When `$effect` Is Correct

Only use `$effect` for synchronizing with **external systems** that Svelte's reactivity doesn't control:

1. **DOM measurements** — reading element dimensions after render
2. **Subscriptions** — WebSocket connections, event listeners, IntersectionObserver
3. **Third-party widgets** — initializing non-Svelte libraries that need a DOM node
4. **Browser APIs** — media queries, geolocation watchers

```svelte
<script lang="ts">
	// CORRECT: External system sync (subscription)
	let ref: HTMLDivElement;

	$effect(() => {
		const observer = new IntersectionObserver(([entry]) => {
			isVisible = entry.isIntersecting;
		});
		observer.observe(ref);
		return () => observer.disconnect();
	});
</script>

<div bind:this={ref}></div>
```

## Use `onMount` for One-Time Lifecycle Work

Don't conflate lifecycle hooks with reactive effects:

```svelte
<script lang="ts">
	import { onMount } from 'svelte';

	// GOOD — one-time setup with cleanup
	onMount(() => {
		const controller = new AbortController();
		initThirdPartyLib(ref, { signal: controller.signal });
		return () => controller.abort();
	});

	// BAD — $effect for one-time work
	$effect(() => {
		initThirdPartyLib(ref);
	});
</script>
```

## Common Anti-Patterns

### Deriving state from reactive values

```svelte
<script lang="ts">
	// BAD — $effect to sync derived state
	let fullName = $state('');
	$effect(() => {
		fullName = `${firstName} ${lastName}`;
	});

	// GOOD — $derived
	const fullName = $derived(`${firstName} ${lastName}`);

	// GOOD — $derived.by for O(n) operations
	const sorted = $derived(items.toSorted((a, b) => a.name.localeCompare(b.name)));
</script>
```

### Responding to user actions

```svelte
<script lang="ts">
	// BAD — $effect reacting to state from user action
	let submitted = $state(false);
	$effect(() => {
		if (submitted) {
			sendAnalytics('form_submit');
			goto('/success');
		}
	});

	function handleSubmit() {
		submitted = true;
	}

	// GOOD — handle everything in the event handler
	function handleSubmit() {
		sendAnalytics('form_submit');
		goto('/success');
	}
</script>
```

### Fetching data reactively

```svelte
<script lang="ts">
	// BAD — $effect to fetch
	$effect(() => {
		if (isConnected) fetchData();
	});

	// BAD — trigger from event handler
	function handleConnect() {
		isConnected = true;
		fetchData();
	}

	// GOOD - use an async management library like TanStack Query
</script>
```

### Resetting state when props change

```svelte
<script lang="ts">
	// BAD — $effect to reset state
	let { items }: Props = $props();
	let selection = $state<string | null>(null);
	$effect(() => {
		items; // track
		selection = null;
	});
</script>

<!-- GOOD — use {#key} to remount the component -->
{#key categoryId}
	<ItemList {items} />
{/key}
```

### Chaining effects

```svelte
<script lang="ts">
	// BAD — cascading $effects
	$effect(() => { fetchA().then(v => { a = v; }); });
	$effect(() => { if (a) fetchB(a).then(v => { b = v; }); });
	$effect(() => { if (b) fetchC(b).then(v => { c = v; }); });

	// GOOD — single event handler
	async function handleLoad() {
		const a = await fetchA();
		const b = await fetchB(a);
		const c = await fetchC(b);
		state = { a, b, c };
	}

	// BETTER - use an async management library like TanStack Query
</script>
```

## Decision Checklist

Before writing `$effect`, ask:

1. **Can this be derived?** → Use `$derived` or `$derived.by()`
2. **Is this triggered by a user action?** → Put it in the event handler
3. **Is this a data fetch?** → Use TanStack Svelte Query or trigger from event handler
4. **Does state need to reset?** → Use `{#key}` to remount the component
5. **Is this one-time lifecycle setup?** → Use `onMount`
6. **Am I syncing with an external system Svelte doesn't control?** → `$effect` is correct
