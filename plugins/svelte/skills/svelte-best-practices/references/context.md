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

```svelte
<!-- BAD: one direct child does not need ambient state. -->
<Toolbar {session} />
```

Keep state and actions in a context boundary with one coherent owner. Do not use context as a hidden global parameter channel or an external-store substitute.
