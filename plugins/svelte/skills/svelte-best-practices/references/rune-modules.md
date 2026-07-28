# Rune Modules

Create a rune module only when it owns reusable reactive behavior. Name it `*.svelte.ts` or `*.svelte.js` so Svelte compiles its runes; do not hide rune state in an ordinary module.

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
