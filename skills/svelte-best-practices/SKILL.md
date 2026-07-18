---
name: svelte-best-practices
description: Svelte 5 component patterns with runes, minimal state, and type-safe conventions. Use when creating components, managing state, or handling side effects.
---

# Svelte Best Practices

Core Svelte 5 patterns for component design, state management, and optimization.

## Documentation

- If available, use the `svelte` MCP server for Svelte documentation and autofixing.

## References

- [Side Effects](./references/side-effects.md) — Why `$effect` is almost never necessary
- [Conditional State](./references/conditional-state.md) — Mount `$state` only when needed
- [Async Data Loading](./references/async-data-loading.md) — Outer-loader pattern for data fetching
- [Form Handling](./references/form-handling.md) — `use:enhance` + form actions (SPA fallback: `onsubmit` + TanStack Query)

## Runes Only, No Stores

Runes replace stores for component and application state. Don't introduce `writable`/`derived` stores in new code:

```typescript
// BAD - store indirection
import { writable } from 'svelte/store';
const count = writable(0);

// GOOD - rune
let count = $state(0);
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

Model multi-step flows as one discriminated union, not parallel state variables that can desynchronize:

```svelte
<script lang="ts">
	// BAD - parallel states can contradict each other
	let isConnecting = $state(false);
	let lobbyId = $state<string>();
	let error = $state<Error>();

	// GOOD - one state, every combination valid by construction
	let lobby = $state<LobbyState>({ status: LobbyStatus.Idle });
</script>
```

```typescript
const enum LobbyStatus {
	Idle = 0,
	Active = 1,
}

interface LobbyIdle {
	status: LobbyStatus.Idle;
}

interface LobbyActive {
	status: LobbyStatus.Active;
	lobbyId: string;
}

type LobbyState = LobbyIdle | LobbyActive;
```

Handle transitions with an exhaustive `switch` (see the `typescript-best-practices` skill).

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

Extend the native element's attribute type, expose a bindable `ref`, and forward `...restProps` — the same wrapping convention as `shadcn-svelte-best-practices`:

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

## Snippets, Not Slots

Use snippets and `{@render}` for component composition, never legacy `<slot>`s — snippets are explicit, typed props (`Snippet`) instead of implicit children channels.

## Context API

Use `createContext` (requires `svelte >= 5.40`) over raw `getContext`/`setContext` — the tuple is typed end-to-end and its getter throws when no parent has set the context, replacing manual guards:

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
