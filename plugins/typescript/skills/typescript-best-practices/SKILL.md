---
name: typescript-best-practices
description: Opinionated TypeScript conventions for inference, exhaustiveness, and strict index access. Use when writing or reviewing TypeScript code.
---

# TypeScript Best Practices

Opinionated conventions for type-safe TypeScript. Every rule exists to let the compiler prove correctness instead of trusting the author.

## Inference over Annotation

Let TypeScript infer types wherever inference produces the correct type. Explicit annotations drift from the implementation and silently mask errors.

```typescript
// BAD - explicit return type
function getUsers(): User[] {
	return db.select().from(users);
}

// GOOD - inferred return type
function getUsers() {
	return db.select().from(users);
}
```

When a constructor or function needs help, pass generic arguments instead of annotating the variable:

```typescript
// BAD - type annotation
const m: Map<string, User> = new Map();

// GOOD - generic argument
const m = new Map<string, User>();
```

### Exception: Annotate When Inference Widens

Inference infers the _initial_ value's type, not the _intended_ union. Annotate whenever the intended type is a union that inference would widen or narrow incorrectly:

```typescript
// useState infers 'idle' from the initial value, rejecting later transitions
const [state, setState] = useState<'idle' | 'loading' | 'error'>('idle');

// createContext infers null, rejecting real values
const Context = createContext<UserContextValue | null>(null);
```

The same widening bites ternaries over string literals, which widen to `string`:

```typescript
// BAD - viewMode is string, not 'list' | 'detail'
const viewMode = isDetail ? 'detail' : 'list';

// GOOD - as const preserves the literal union
const viewMode = isDetail ? ('detail' as const) : ('list' as const);
```

## Exhaustive `switch` over Object Maps

Object maps silently return `undefined` for unknown keys. A `switch` with a throwing `default` turns unhandled variants into loud runtime errors and keeps every case visible to the reader.

```typescript
// BAD - object map, unknown status yields undefined
const statusColors = {
	SUCCESS: 'green',
	FAILED: 'red',
	PENDING: 'yellow',
};
const color = statusColors[status];

// GOOD - exhaustive switch with throwing default
function getStatusColor(status: StatusEnum) {
	switch (status) {
		case StatusEnum.SUCCESS:
			return 'green';
		case StatusEnum.FAILED:
			return 'red';
		case StatusEnum.PENDING:
			return 'yellow';
		default:
			throw new Error(`Unknown status: ${status}`);
	}
}
```

## No Non-null Assertions

The `!` operator is a lie to the compiler. Guard and throw so the failure is explicit and carries context.

```typescript
// BAD - non-null assertion
const user = users.find(u => u.id === id)!;

// GOOD - runtime validation
const user = users.find(u => u.id === id);
if (typeof user === 'undefined') throw new Error(`User not found: ${id}`);
```

## No Type Casts

`as` asserts without proof. At trust boundaries (network responses, JSON columns, form data), validate instead of casting. See the `valibot-best-practices` skill for schema patterns.

```typescript
// BAD - type cast
const data = response as UserData;

// GOOD - validation
const result = v.safeParse(UserDataSchema, response);
if (!result.success) throw new Error('Invalid response');
const data = result.output;
```

## Destructuring over Index Access

```typescript
// BAD - array index
const first = arr[0];

// GOOD - destructuring
const [first] = arr;
```

## `noUncheckedIndexedAccess` Playbook

When tsconfig enables `noUncheckedIndexedAccess: true`, all bracket-access and destructured array results get `| undefined`. This catches real bugs but requires specific handling patterns.

### Array index access — guard before use

```typescript
// BAD - arr[i] is T | undefined, cannot pass to fn(arg: T)
insertMention(filteredAnalysts[selectedIndex]);

// GOOD - guard narrows to T
const selected = filteredAnalysts[selectedIndex];
if (typeof selected !== 'undefined') insertMention(selected);
```

### String `.split()` results — destructure with fallback

```typescript
// BAD - split('@')[0] is string | undefined
const localPart = email.split('@')[0];

// GOOD - destructure (still string | undefined), then provide fallback
const [localPart] = email.split('@');
const displayName = fullName ?? localPart ?? email;
```

### Regex match groups — provide fallback

```typescript
// BAD - match[1] is string | undefined, match.groups?.name is string | undefined
content: match.groups?.name ?? match[1];

// GOOD - chain fallback to guaranteed string
content: match.groups?.name ?? match[1] ?? '';
```
