# Side Effects in React

## Core Principle

**`useEffect` is almost never necessary.** 99% of side effects belong in event handlers, not in effects.

## Effects vs Events

|                  | Event Handlers                                    | `useEffect`                                          |
| ---------------- | ------------------------------------------------- | ---------------------------------------------------- |
| **Triggered by** | User action (click, submit, navigate)             | Render / dependency change                           |
| **When to use**  | Mutations, navigation, form submission, API calls | External system sync only                            |
| **Examples**     | `onClick`, `onSubmit`, `onChange`                 | DOM measurements, subscriptions, third-party widgets |

## When `useEffect` Is Correct

Only use `useEffect` for synchronizing with **external systems** that React doesn't control:

1. **DOM measurements** — reading element dimensions after render
2. **Subscriptions** — WebSocket connections, event listeners, IntersectionObserver
3. **Third-party widgets** — initializing non-React libraries that need a DOM node
4. **Browser APIs** — media queries, geolocation watchers

```tsx
// CORRECT: External system sync (subscription)
useEffect(() => {
	const ws = new WebSocket(url);
	ws.onmessage = e => setMessages(prev => [...prev, e.data]);
	return () => ws.close();
}, [url]);

// CORRECT: DOM measurement
useEffect(() => {
	const { height } = ref.current.getBoundingClientRect();
	setContentHeight(height);
}, [content]);
```

## Common Anti-Patterns

### Deriving state from props/state

```tsx
// BAD - useEffect to sync derived state
const [fullName, setFullName] = useState('');
useEffect(() => {
	setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// GOOD - derive directly
const fullName = `${firstName} ${lastName}`;

// GOOD - useMemo for O(n) derivations
const sorted = useMemo(() => items.toSorted((a, b) => a.name.localeCompare(b.name)), [items]);
```

### Fetching data on mount

```tsx
// BAD - manual fetch in useEffect
useEffect(() => {
	fetchUser(id).then(setUser);
}, [id]);

// GOOD - use a data fetching library (TanStack Query, SWR, etc.)
const { data: user } = useQuery({
	queryKey: ['user', id],
	queryFn: async () => await fetchUser(id),
});
```

### Responding to user actions

```tsx
// BAD - useEffect reacting to state change from user action
const [submitted, setSubmitted] = useState(false);
useEffect(() => {
	if (submitted) {
		sendAnalytics('form_submit');
		navigate('/success');
	}
}, [submitted]);

function handleSubmit() {
	setSubmitted(true);
}

// GOOD - handle everything in the event handler
function handleSubmit() {
	sendAnalytics('form_submit');
	navigate('/success');
}
```

### Resetting state when props change

```tsx
// BAD - useEffect to reset state on prop change
useEffect(() => {
	setSelection(null);
}, [items]);

// GOOD - use a key to remount the component
<ItemList key={categoryId} items={items} />;
```

### Chaining effects

```tsx
// BAD - cascading useEffects
useEffect(() => {
	fetchA().then(setA);
}, []);
useEffect(() => {
	if (a) fetchB(a).then(setB);
}, [a]);
useEffect(() => {
	if (b) fetchC(b).then(setC);
}, [b]);

// GOOD - single event handler or data fetching library
async function handleLoad() {
	const a = await fetchA();
	const b = await fetchB(a);
	const c = await fetchC(b);
	setState({ a, b, c });
}
```

## Decision Checklist

Before writing `useEffect`, ask:

1. **Can this be derived?** → Compute it during render or with `useMemo`
2. **Is this triggered by a user action?** → Put it in the event handler
3. **Is this a data fetch?** → Use TanStack Query or equivalent
4. **Does state need to reset?** → Use a `key` prop to remount
5. **Am I syncing with an external system React doesn't control?** → `useEffect` is correct
