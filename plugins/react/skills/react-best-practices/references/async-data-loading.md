# Async Data Loading in React

## Core Principle

**Separate data-fetching from presentation.** Use a Loader component that handles query states (loading, error, empty) and an Inner component that receives resolved data as props.

## Loader/Inner Pattern

```text
ComponentLoader  → runs queries, handles loading/error/empty states
ComponentInner   → pure presentation, receives resolved data as props
```

### Basic Structure

```tsx
// Inner: pure presentation, easily testable
interface InnerProps {
	data: JobData;
	onAction: (id: string) => void;
}

function JobDetailInner({ data, onAction }: InnerProps) {
	// useMemo works cleanly — no optional chaining needed
	const processed = useMemo(() => data.items.map(transform), [data.items]);
	return <DataDisplay items={processed} onAction={onAction} />;
}

// Loader: handles all async states
function JobDetailLoader({ jobId }: { jobId: string }) {
	const { data, isPending, isError, error } = useQuery({
		queryKey: ['job', jobId],
		queryFn: () => fetchJob(jobId),
	});

	if (isPending) return <Skeleton />;
	if (isError) return <ErrorBanner error={error} />;
	if (typeof data === 'undefined') return <NotFound />;

	return <JobDetailInner data={data} onAction={handleAction} />;
}
```

## Why This Pattern?

1. **Testability**: Inner component can be tested with mock props — no query mocking
2. **Type narrowing**: Inner receives non-null types, eliminating optional chaining in computations
3. **Clean memoization**: `useMemo` in Inner only runs when data exists
4. **Separation of concerns**: Loading logic isolated from presentation

## In Dialogs (Conditional State + Loader/Inner)

```tsx
// Shell — controls open state
function EditDialog({ itemId, open, onOpenChange, onSuccess }: Props) {
	return (
		<Dialog open={open} onOpenChange={onOpenChange}>
			<DialogContent>{open && <EditLoader itemId={itemId} onSuccess={onSuccess} />}</DialogContent>
		</Dialog>
	);
}

// Loader — fetches data, gates rendering
function EditLoader({ itemId, onSuccess }: { itemId: string; onSuccess: () => void }) {
	const { data, isPending } = useQuery({
		queryKey: ['item', itemId],
		queryFn: () => fetchItem(itemId),
	});

	if (isPending) return <Spinner />;
	if (typeof data === 'undefined') return <NotFound />;

	return <EditForm initialData={data} onSuccess={onSuccess} />;
}

// Form — pure, receives resolved data, seeds state
function EditForm({ initialData, onSuccess }: { initialData: Item; onSuccess: () => void }) {
	const [name, setName] = useState(initialData.name);
	// Safe: component only mounts when data is ready
}
```

## Early Return Pattern

Always handle states in order: loading → error → empty → success.

```tsx
function Component({ id }: Props) {
	const { data, isPending, isError, error } = useQuery({ ... });

	if (isPending) return <LoadingState />;
	if (isError) return <ErrorState error={error} />;
	if (typeof data === 'undefined' || data.length === 0) return <EmptyState />;

	return <DataDisplay data={data} />;
}
```

## Key Points

- Gate on data presence (`typeof data === 'undefined'`), not truthiness — `null` may be a valid resolved value
- Mutations stay in the Inner/Form component — only **queries** move to the Loader
- Inner component only mounts when data exists, so all props are guaranteed non-null
- Combine with [Conditional State](./conditional-state.md) for dialogs/sheets
