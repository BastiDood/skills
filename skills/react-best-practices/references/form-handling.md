# Form Handling in React

## Core Principle

**`decode-formdata` + Valibot for client-side FormData validation.** Synchronous handlers, submitter disabling, per-call mutation callbacks. No `useEffect`.

## Stack

- `decode-formdata` — Decodes `FormData` into typed objects
- `valibot` — Schema validation
- TanStack Query `useMutation` — Mutation management

## Core Pattern

```tsx
import * as v from 'valibot';
import { decode } from 'decode-formdata';

// Schema at module level
const CreateItemSchema = v.object({
	name: v.pipe(v.string(), v.minLength(1)),
	amount: v.number(),
	category: v.optional(v.pipe(v.string(), v.minLength(1))),
});

function CreateItemForm({ onSuccess }: { onSuccess: () => void }) {
	const { mutate } = useCreateItem();

	return (
		<form
			onSubmit={event => {
				event.preventDefault();
				const { currentTarget, submitter } = event.nativeEvent as SubmitEvent;
				const form = currentTarget as HTMLFormElement;

				// Assert submitter is a button
				if (!(submitter instanceof HTMLButtonElement))
					throw new Error('submitter must be a button');

				// Disable submitter immediately
				submitter.disabled = true;

				const data = new FormData(form);
				const parsed = v.parse(CreateItemSchema, decode(data, { numbers: ['amount'] }));

				mutate(parsed, {
					onSuccess,
					onSettled: () => {
						submitter.disabled = false;
					},
				});
			}}
		>
			<input name="name" type="text" required />
			<input name="amount" type="number" required />
			<input name="category" type="text" />
			<button type="submit">Create</button>
		</form>
	);
}
```

## Field Naming

- **camelCase** for all form field `name` attributes (e.g., `studentNumber`, `closesAt`)
- Schema property names must match field names exactly

## `decode()` Options

Specify non-string types explicitly:

```tsx
decode(data, {
	numbers: ['amount', 'quantity'],
	dates: ['datedAt', 'expiresAt'],
	arrays: ['tags', 'categories'],
});
```

## Common Valibot Schema Patterns

```typescript
// Required non-empty string
v.pipe(v.string(), v.minLength(1));

// Optional string (empty string stays as-is — filter at decode level)
v.optional(v.pipe(v.string(), v.minLength(1)));

// Email
v.pipe(v.string(), v.email());

// Array of non-empty strings
v.array(v.pipe(v.string(), v.minLength(1)));

// Number / Date
v.number();
v.date();
```

## Submitter Disabling (Required)

Every submit handler must disable the submit button during submission to prevent double submits.

### Key Points

- **Always call `event.preventDefault()`**
- **Use `mutate`, never `mutateAsync`** — let per-call `onSuccess`/`onError`/`onSettled` handle outcomes
- **Assert submitter is `HTMLButtonElement`** — never use non-null assertion
- **Handler is synchronous** — no `async`, no `try/finally`
- **`onSettled` re-enables the submitter** (runs on both success and error)
- **`onSuccess` handles post-success logic** (form reset, closing dialog, etc.)

### Multiple Submit Buttons

Use a `useState` so all buttons share the disabled state:

```tsx
function MultiActionForm() {
	const [disabled, setDisabled] = useState(false);

	return (
		<form
			onSubmit={event => {
				event.preventDefault();
				setDisabled(true);
				// ... parse and mutate
				mutate(parsed, {
					onSettled: () => setDisabled(false),
				});
			}}
		>
			<button type="submit" name="action" value="promote" disabled={disabled}>
				Promote
			</button>
			<button type="submit" name="action" value="remove" disabled={disabled}>
				Remove
			</button>
		</form>
	);
}
```

## Anti-Patterns

```tsx
// BAD - useEffect to handle form submission result
useEffect(() => {
	if (mutation.isSuccess) onSuccess();
}, [mutation.isSuccess]);

// GOOD - per-call onSuccess callback
mutate(data, { onSuccess: () => onSuccess() });
```

```tsx
// BAD - async handler with try/finally
const handleSubmit = async e => {
	e.preventDefault();
	try {
		await mutateAsync(data);
	} finally {
		setDisabled(false);
	}
};

// GOOD - synchronous handler with per-call callbacks
mutate(data, {
	onSettled: () => {
		submitter.disabled = false;
	},
});
```
