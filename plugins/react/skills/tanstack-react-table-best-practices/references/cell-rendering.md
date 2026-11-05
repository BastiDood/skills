# Cell Rendering

Render column definitions through `FlexRender`. Never hand-roll `flexRender`: do not read `columnDef.cell`, `header`, `footer`, or `aggregatedCell` and then invoke, stringify, or place it in JSX yourself.

```tsx
import { tableFeatures, type Cell, type ReactTable, type TableState } from '@tanstack/react-table';

interface Person {
	name: string;
}

const features = tableFeatures({});

interface CellProps {
	cell: Cell<typeof features, Person, unknown>;
	table: ReactTable<typeof features, Person, TableState<typeof features>>;
}

// BAD: invoking the definition yourself renders `cell` for aggregated rows and content for placeholders.
function HandRolledCell({ cell }: CellProps) {
	const definition = cell.column.columnDef.cell;
	return <td>{typeof definition === 'function' ? definition(cell.getContext()) : definition}</td>;
}

// GOOD: the owning table supplies the cell context and preserves feature-local types.
function BodyCell({ cell, table }: CellProps) {
	return (
		<td>
			<table.FlexRender cell={cell} />
		</td>
	);
}
```

`FlexRender` selects `aggregatedCell` for an aggregated cell and renders nothing for a placeholder cell. The standalone `flexRender(Comp, props)` function does neither, so it is not a substitute for the component.

Apply the same pattern to headers and footers through the `header` and `footer` props. `FlexRender` does not suppress placeholder headers; check `header.isPlaceholder` before rendering one.
