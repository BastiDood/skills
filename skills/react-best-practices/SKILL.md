---
name: react-best-practices
description: React component patterns with minimal state, proper memoization, and type-safe conventions. Use when creating components, managing state, or optimizing renders.
---

# React Best Practices

Core React patterns for component design, state management, and optimization.

## References

- [Side Effects](./references/side-effects.md) — Why `useEffect` is almost never necessary
- [Conditional State](./references/conditional-state.md) — Mount `useState` only when needed
- [Async Data Loading](./references/async-data-loading.md) — Loader/Inner pattern for data fetching
- [Form Handling](./references/form-handling.md) — `decode-formdata` + `valibot` validation

## State Philosophy

Avoid state variables. Prefer derived values and props. Scope state to the smallest subtree that needs it. Use discriminated unions for complex state.

### Derived Values Over State

The only exception to this rule is when the derivation is a non-constant-time operation — that is, O(n) or worse.

```tsx
// BAD - unnecessary state
const [fullName, setFullName] = useState('');
useEffect(() => {
	setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// GOOD - derived value
const fullName = `${firstName} ${lastName}`;
```

### Component Boundaries for State Scoping

```tsx
// BAD - state too high
function Dialog() {
	const [formData, setFormData] = useState({});
	return (
		// We know that `DialogContent` is only conditionally mounted.
		<DialogContent>
			<Form data={formData} onChange={setFormData} />
		</DialogContent>
	);
}

// GOOD - state in conditional subtree
function Dialog() {
	return (
		// State belongs to the inner component for conditional state mounting.
		<DialogContent>
			<FormWithState />
		</DialogContent>
	);
}
```

### Discriminated Union State Machines

```tsx
// BAD - multiple related states
const [isLoading, setIsLoading] = useState(false);
const [error, setError] = useState<Error | null>(null);
const [data, setData] = useState<Data | null>(null);

// GOOD - discriminated union
type State =
	| { status: 'idle' }
	| { status: 'loading' }
	| { status: 'error'; error: Error }
	| { status: 'success'; data: Data };

const [state, setState] = useState<State>({ status: 'idle' });
```

## Memoization

Required for any O(n) operation. Memoize atomically to minimize dependency arrays.

### When to `useMemo`

```tsx
// Required - O(n) operation
const total = useMemo(() => items.reduce((sum, item) => sum + item.value, 0), [items]);

// Not needed - O(1) operation
const isActive = status === 'active';
```

### Atomic Memoization

Minimize dependency arrays by memoizing atomically:

```tsx
// BAD - coupled dependencies
const { client, total } = useMemo(
	() => ({
		client: createClient(url),
		total: items.reduce((s, i) => s + i, 0),
	}),
	[url, items], // Any change recomputes both
);

// GOOD - atomic memoization
const client = useMemo(() => createClient(url), [url]);
const total = useMemo(() => items.reduce((s, i) => s + i, 0), [items]);
```

### Derived Values as Dependencies

```tsx
// BAD - recomputes when either changes
const nodes = useMemo(
	() => render(query.isPending || mutation.isPending),
	[query.isPending, mutation.isPending],
);

// GOOD - single derived dependency
const isPending = query.isPending || mutation.isPending;
const nodes = useMemo(() => render(isPending), [isPending]);
```

## Conditional Logic

Use affirmative logic, explicit conditionals, and ternaries over `&&`. Early returns for guard clauses.

### Affirmative Logic

```tsx
// BAD - double negative
if (!isInvalid) { ... }

// GOOD - affirmative
if (isValid) { ... }
```

### Conditional Rendering

```tsx
// BAD - && can render 0 or empty string
{
	count && <Badge count={count} />;
}

// GOOD - explicit ternary
{
	count > 0 ? <Badge count={count} /> : null;
}
```

### Early Returns

```tsx
function Component({ data, isLoading, error }: Props) {
	if (isLoading) return <Spinner />;
	if (error) return <ErrorMessage error={error} />;
	if (typeof data === 'undefined') return <Empty />;
	return <DataDisplay data={data} />;
}
```

## Context for Forwarded-Only Props

When intermediate components would only forward a prop (never use it), lift it into context instead of threading it through every layer:

```tsx
const ValueContext = createContext<{ value: string; setValue: (v: string) => void } | null>(null);

function Provider({ children }: { children: ReactNode }) {
	const [value, setValue] = useState('');
	const ctx = useMemo(() => ({ value, setValue }), [value]);
	return <ValueContext.Provider value={ctx}>{children}</ValueContext.Provider>;
}

function useValue() {
	const ctx = useContext(ValueContext);
	if (ctx === null) throw new Error('useValue must be within Provider');
	return ctx;
}
```
