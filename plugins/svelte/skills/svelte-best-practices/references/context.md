# Context

Use `createContext` for stable state or behavior owned by a component subsystem and consumed at multiple depths. Keep short, explicit composition paths as props. Forwarding one prop does not justify context.

Do not use raw `getContext` and `setContext`. A required context getter must fail when its owning parent is absent.

```typescript
import { createContext } from 'svelte';

interface WorkspaceSession {
	refresh(): Promise<void>;
}

// GOOD: a workspace subtree owns one capability shared at several depths.
export const [getWorkspaceSession, setWorkspaceSession] = createContext<WorkspaceSession>();
```

For a session used by just one direct child, compare the parent composition:

```svelte
<!-- BAD: introduces ambient state for a direct prop relationship. -->
<script lang="ts">
	import { setWorkspaceSession } from './workspace-context';
	const { session } = $props();
	setWorkspaceSession({ refresh: () => session.refresh() });
</script>

<Toolbar />
```

```svelte
<!-- GOOD: the direct relationship stays explicit. -->
<script lang="ts">
	const { session } = $props();
</script>

<Toolbar {session} />
```

Keep state and actions in a context boundary with one coherent owner. Do not use context as a hidden global parameter channel or an external-store substitute.
