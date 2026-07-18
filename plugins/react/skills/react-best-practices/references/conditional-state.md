# Conditional State in React

## Core Principle

**Mount `useState` only when needed.** Wrap stateful components in conditional renders so state is lazily initialized on mount and automatically destroyed on unmount. No stale state, no manual cleanup.

## Why

- `useState` initializes when the component mounts
- If the component unmounts, all state is destroyed
- Conditional rendering (`{open ? <Form /> : null}`) gives you **automatic state lifecycle**
- No need for `useEffect` to reset state — just unmount and remount

## Pattern: Dialog/Sheet with Lazy State

```tsx
// Shell — controls open state, contains NO form state
function EditDialog({ open, onOpenChange, onSuccess }: Props) {
	return (
		<Dialog open={open} onOpenChange={onOpenChange}>
			<DialogContent>
				<DialogHeader>
					<DialogTitle>Edit Item</DialogTitle>
				</DialogHeader>
				{open ? <EditForm onSuccess={onSuccess} /> : null}
			</DialogContent>
		</Dialog>
	);
}

// Form — owns state, only mounts when dialog is open
function EditForm({ onSuccess }: { onSuccess: () => void }) {
	// These are lazily initialized when the dialog opens
	const [name, setName] = useState('');
	const [email, setEmail] = useState('');

	// When dialog closes, this component unmounts → state is destroyed
	// When dialog reopens, fresh state is created
	return (
		<form onSubmit={handleSubmit}>
			<Input value={name} onChange={e => setName(e.target.value)} />
			<Input value={email} onChange={e => setEmail(e.target.value)} />
			<Button type="submit">Save</Button>
		</form>
	);
}
```

## Why Not Reset State in `useEffect`?

```tsx
// BAD — manual reset, easy to miss new fields, stale state bugs
function EditForm({ open }: Props) {
	const [name, setName] = useState('');
	const [email, setEmail] = useState('');

	useEffect(() => {
		if (open) {
			setName('');
			setEmail('');
		}
	}, [open]);
}

// GOOD — conditional rendering handles it automatically
{
	open ? <EditForm /> : null;
}
```

## Beyond Dialogs

This pattern applies to any conditionally visible UI:

- **Tabs**: Each tab panel can be a separate component that mounts/unmounts
- **Accordions**: Content mounts when expanded, unmounts when collapsed
- **Wizards/Steps**: Each step is a component with its own state
- **Drawers/Sheets**: Same as dialogs — content mounts on open

```tsx
// Tab panels with lazy state
function TabContent({ activeTab }: { activeTab: string }) {
	switch (activeTab) {
		case 'settings':
			return <SettingsPanel />; // Mounts with fresh state
		case 'profile':
			return <ProfilePanel />; // Mounts with fresh state
		default:
			return null;
	}
}
```

## With Async Data (Loader/Inner)

Combine conditional state with the Loader/Inner pattern for data-dependent forms:

```tsx
function EditDialog({ itemId, open, onOpenChange }: Props) {
	return (
		<Dialog open={open} onOpenChange={onOpenChange}>
			<DialogContent>{open ? <EditLoader itemId={itemId} /> : null}</DialogContent>
		</Dialog>
	);
}

// Loader fetches data, gates rendering
function EditLoader({ itemId }: { itemId: string }) {
	const { data, isPending } = useQuery({
		queryKey: ['item', itemId],
		queryFn: () => fetchItem(itemId),
	});

	if (isPending) return <Spinner />;
	if (typeof data === 'undefined') return <NotFound />;

	return <EditForm initialData={data} />;
}

// Form receives resolved data, seeds state safely
function EditForm({ initialData }: { initialData: Item }) {
	const [name, setName] = useState(initialData.name);
	// Safe: component only mounts when data is ready
}
```

## Key Points

- **Conditional rendering = automatic state lifecycle** — no manual resets needed
- **Parent controls open state**; child owns form state
- **`key` prop** is an alternative for resetting state without unmounting: `<Form key={itemId} />`
- **Never use `useEffect` to reset form state** — let mount/unmount handle it
