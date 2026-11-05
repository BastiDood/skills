# Cell Rendering

Render column definitions through `FlexRender`. Never hand-roll `flexRender`: do not read `columnDef.cell`, `header`, `footer`, or `aggregatedCell` and then invoke, stringify, or interpolate it yourself.

```svelte
<script lang="ts">
	import { tableFeatures, type Cell } from '@tanstack/svelte-table';

	interface Person {
		name: string;
	}

	const features = tableFeatures({});
	let { cell }: { cell: Cell<typeof features, Person, unknown> } = $props();
	const definition = $derived(cell.column.columnDef.cell);
</script>

<!-- BAD: invoking the definition yourself renders `cell` for aggregated rows and content for placeholders. -->
<td>{typeof definition === 'function' ? String(definition(cell.getContext())) : definition}</td>
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

`FlexRender` selects `aggregatedCell` for an aggregated cell and renders nothing for a placeholder cell. Apply the same pattern to headers and footers through the `header` and `footer` props. `FlexRender` does not suppress placeholder headers; check `header.isPlaceholder` before rendering one.

Define component and snippet cells with the adapter helpers; `FlexRender` resolves both through the cell context.

```typescript
import { renderComponent } from '@tanstack/svelte-table';
import StatusCell from './status-cell.svelte';

// GOOD: return a render description; let `FlexRender` own component creation and updates.
const statusCell = renderComponent(StatusCell, { status: 'active' });
```

```svelte
<script lang="ts">
	import { renderSnippet } from '@tanstack/svelte-table';

	// GOOD: pass one explicit parameter object; `FlexRender` owns snippet invocation.
	const statusCell = renderSnippet(statusLabel, { status: 'active' });
</script>

{#snippet statusLabel({ status }: { status: string })}
	<span>{status}</span>
{/snippet}
```

Return either helper from a column `cell`, `header`, or `footer` definition rather than attempting to invoke rendering yourself.
