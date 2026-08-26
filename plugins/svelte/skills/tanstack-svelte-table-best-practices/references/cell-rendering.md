# Cell Rendering

Render column definitions through the adapter renderer. A definition is schema, not displayable content.

```svelte
<script lang="ts">
	import { tableFeatures, type Cell } from '@tanstack/svelte-table';

	interface Person {
		name: string;
	}

	const features = tableFeatures({});
	let { cell }: { cell: Cell<typeof features, Person, unknown> } = $props();
</script>

<!-- BAD: coercing the definition renders schema data or function source, not the cell result. -->
<td>{String(cell.column.columnDef.cell)}</td>
```

```svelte
<script lang="ts">
	import { FlexRender, tableFeatures, type Cell } from '@tanstack/svelte-table';

	interface Person {
		name: string;
	}

	const features = tableFeatures({});
	let { cell }: { cell: Cell<typeof features, Person, unknown> } = $props();
</script>

<!-- GOOD: the adapter renderer supplies the cell context and preserves feature-local types. -->
<td><FlexRender {cell} /></td>
```

Apply the same pattern to headers and footers. Do not reconstruct rendering contexts manually.

Define component and snippet cells with the adapter helpers; `FlexRender` resolves both through the cell context.

```typescript
import { renderComponent } from '@tanstack/svelte-table';
import StatusCell from './status-cell.svelte';

// GOOD: return a render description; let FlexRender own component creation and updates.
const statusCell = renderComponent(StatusCell, { status: 'active' });
```

```svelte
<script lang="ts">
	import { renderSnippet } from '@tanstack/svelte-table';

	// GOOD: pass one explicit parameter object; FlexRender owns snippet invocation.
	const statusCell = renderSnippet(statusLabel, { status: 'active' });
</script>

{#snippet statusLabel({ status }: { status: string })}
	<span>{status}</span>
{/snippet}
```

Return either helper from a column `cell`, `header`, or `footer` definition rather than attempting to invoke rendering yourself.
