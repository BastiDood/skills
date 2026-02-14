---
name: svelte-best-practices
description: Svelte 5 component patterns with runes, minimal state, and type-safe conventions. Use when creating components, managing state, or working with Svelte-specific patterns.
---

# Svelte Best Practices

Core Svelte 5 patterns for component design, state management, and optimization.

## Documentation

- If available, use the `svelte` MCP server for Svelte documentation and autofixing.

## References

- [Side Effects](references/side-effects.md) — Why `$effect` is almost never necessary
- [Conditional State](references/conditional-state.md) — Mount `$state` only when needed
- [Async Data Loading](references/async-data-loading.md) — Outer-loader pattern for data fetching
- [Form Handling](references/form-handling.md) — `use:enhance` + form actions (SPA fallback: `onsubmit` + TanStack Query)

## Key Rules

1. **Runes only** — No Svelte stores
2. **No `$effect`** — Side effects in event handlers only
3. **Discriminated unions** — `const enum` + `switch` for state machines
4. **Runtime assertions** — Never use `!` non-null assertion

## Runes

```svelte
<script lang="ts">
	let count = $state(0);
	let items = $state<string[]>([]);

	const doubled = $derived(count * 2);
	const total = $derived.by(() => {
		// Useful for multi-statement derivations.
		return items.reduce((a, b) => a + b.length, 0);
	});

	let { open = $bindable(false) }: Props = $props();
</script>
```

## Memoization with `$derived`

`$derived` is automatically memoized, but structure derivations atomically to minimize the recomputation surface:

```svelte
<script lang="ts">
	// GOOD — atomic derivations, independent recomputation
	const sortedItems = $derived(items.toSorted((a, b) => a.name.localeCompare(b.name)));
	const total = $derived(items.reduce((sum, i) => sum + i.amount, 0));

	// BAD — bundled derivation, both recompute when either dependency changes
	const computed = $derived({
		sorted: items.toSorted((a, b) => a.name.localeCompare(b.name)),
		total: items.reduce((sum, i) => sum + i.amount, 0),
	});
</script>
```

For complex `O(n)` (or worse) derivations, use `$derived.by()`:

```svelte
<script lang="ts">
	const grouped = $derived.by(() => {
		const map = new Map<string, Item[]>();
		for (const item of items) {
			const group = map.get(item.category) ?? [];
			group.push(item);
			map.set(item.category, group);
		}
		return map;
	});
</script>
```

## Discriminated Union State Machines

```typescript
const enum LobbyStatus {
	Idle = 0,
	Connecting = 1,
	Active = 2,
	Error = 3,
}

interface LobbyIdle {
	status: LobbyStatus.Idle;
}

interface LobbyConnecting {
	status: LobbyStatus.Connecting;
	userName: string;
}

interface LobbyActive {
	status: LobbyStatus.Active;
	lobbyId: string;
}

interface LobbyError {
	status: LobbyStatus.Error;
	error: Error;
}

type LobbyState = LobbyIdle | LobbyConnecting | LobbyActive | LobbyError;
```

Use `switch` for exhaustive handling:

```typescript
switch (state.status) {
	case LobbyStatus.Idle:
		return handleIdle();
	case LobbyStatus.Connecting:
		return handleConnecting(state.userName);
	case LobbyStatus.Active:
		return handleActive(state.lobbyId);
	case LobbyStatus.Error:
		return handleError(state.error);
}
```

## Type Safety

### No Non-null Assertions

```typescript
// BAD
const value = map.get(key)!;

// GOOD
const value = map.get(key);
if (typeof value === 'undefined') throw new Error('Expected value to exist');
```

### Type Inference

```typescript
// BAD - explicit return type
function getStatus(): boolean {
	return session.state.status === LobbyStatus.Active;
}

// GOOD - inferred
function getStatus() {
	return session.state.status === LobbyStatus.Active;
}
```

### Undefined Handling

```typescript
// Prefer omitting arguments when possible
let map = $state<Map>(); // implicitly undefined

// If you must explicitly pass undefined, use void 0
reset(void 0);

// Check with typeof
if (typeof value === 'undefined') throw new Error('Expected value to be defined');
```

## Component Props

```svelte
<script lang="ts">
	import type { HTMLAttributes } from 'svelte/elements';

	interface Props extends HTMLAttributes<HTMLDivElement> {
		variant?: 'default' | 'outline';
		ref?: HTMLDivElement | null;
	}

	let {
		variant = 'default',
		ref = $bindable(null),
		class: className,
		children,
		...restProps
	}: Props = $props();
</script>

<div bind:this={ref} class={cn(variants({ variant }), className)} {...restProps}>
	{@render children?.()}
</div>
```

## Snippets (Not Slots)

```svelte
<!-- Child component -->
<script lang="ts">
	import type { Snippet } from 'svelte';
	let { content }: { content?: Snippet } = $props();
</script>

{@render content?.()}

<!-- Parent -->
{#snippet markerContent()}
	<div class="marker">Custom content</div>
{/snippet}

<Marker content={markerContent} />
```

## Context API

```typescript
import { createContext } from 'svelte';

export const [getSession, setSession] = createContext<Session>();

// +layout.svelte: setSession(new Session());
// Component: const session = getSession();
```

## File Naming

- Default to `.js` files
- Only use `.ts` when TypeScript syntax is necessary
- Use `.svelte.ts` for files with runes outside components
