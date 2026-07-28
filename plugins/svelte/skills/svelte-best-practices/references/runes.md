# Runes and Ownership

Use runes for state owned by the component that reads and changes it. Do not introduce a legacy store merely to avoid choosing an owner for local state.

```svelte
<script lang="ts">
    interface Props {
        initialTitle: string;
    }

	const { initialTitle }: Props = $props();

	// GOOD: this editor owns its draft until it unmounts.
	let title = $state(initialTitle);
</script>
```

```typescript
import { writable } from 'svelte/store';

// BAD: a global store makes the draft's owner and lifetime ambiguous.
export const draft = writable({ title: '' });
```

Use a rune module only when that module owns a cohesive reactive abstraction. Use an external store only when independent subtrees need the same state or selective subscriptions.
