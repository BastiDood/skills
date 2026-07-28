# Cell Rendering

Render column definitions through the v9 renderer. A definition is schema, not displayable content.

```svelte
<!-- BAD: the raw definition bypasses its rendering context. -->
{#each table.getRowModel().rows as row (row.id)}
	<tr>
		{#each row.getVisibleCells() as cell (cell.id)}
			<td>{cell.column.columnDef.cell}</td>
		{/each}
	</tr>
{/each}
```

```svelte
<script lang="ts">
	import { FlexRender } from '@tanstack/svelte-table';
</script>

<!-- GOOD: the renderer receives the table-owned cell context. -->
{#each table.getRowModel().rows as row (row.id)}
	<tr>
		{#each row.getVisibleCells() as cell (cell.id)}
			<td><FlexRender {cell} /></td>
		{/each}
	</tr>
{/each}
```

Use the same renderer for headers and footers. Do not render raw column definitions or reconstruct their contexts manually.
