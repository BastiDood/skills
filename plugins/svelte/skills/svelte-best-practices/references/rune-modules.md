# Rune Modules

A rune module owns cohesive reactive behavior, even for one consumer. Name it `*.svelte.ts` or `*.svelte.js` so Svelte compiles its runes. Keep plain pure utilities in ordinary modules; extract a utility file when its behavior needs separate colocated tests.

```typescript
// BAD: an ordinary module conceals reactive mutable state.
let selectedId = $state<string | undefined>();

// GOOD: one cohesive abstraction owns its reactive state and operations.
export class Selection {
	#selectedId = $state<string | undefined>();

	select(id: string) {
		this.#selectedId = id;
	}

	clear() {
		this.#selectedId = void 0;
	}

	get selectedId() {
		return this.#selectedId;
	}
}
```
