---
name: type-modeling
description: TypeScript type modeling and Valibot schema patterns. Use when defining types, writing validation schemas, or handling nullable/optional fields.
---

# Type Modeling

TypeScript conventions and Valibot schema patterns for type-safe data modeling.

## Valibot Schema Patterns

### Nullability Semantics

Use the correct pattern based on the field's semantics:

```typescript
import * as v from 'valibot';

// Required but can be null (field always present, value may be null)
created_at: v.nullable(v.string()),

// Optional AND nullable (field may be absent OR null)
category: v.nullish(v.string()),

// Optional but non-null (field may be absent, but if present, has value)
source: v.optional(v.string()),
```

### Anti-pattern: Verbose Nullish

Never use `v.optional(v.nullable(...))` - use `v.nullish(...)` instead:

```typescript
// BAD - verbose and redundant
filename: v.optional(v.nullable(v.string())),

// GOOD - concise equivalent
filename: v.nullish(v.string()),
```

### Schema + Type Inference

Define schema once, infer type from it (single source of truth):

```typescript
const KpiDataRowSchema = v.object({
	fiscal_period: v.string(),
	va: v.nullable(v.number()),
	pct_delta: v.nullable(v.number()),
	yoy_va: v.nullish(v.number()),
});

export type KpiDataRow = v.InferOutput<typeof KpiDataRowSchema>;
```

### Discriminated Unions

Use `v.literal` for discriminated union variants:

```typescript
const SuccessResponse = v.object({
	status: v.literal(StatusEnum.SUCCESS),
	data: v.string(),
});

const FailedResponse = v.object({
	status: v.literal(StatusEnum.FAILED),
	error: v.string(),
});

const Response = v.union([SuccessResponse, FailedResponse]);
```

### Safe Parsing for Untrusted Data

Use `v.safeParse` when validating untrusted data (e.g., JSONB columns, user input):

```typescript
const result = v.safeParse(KpiDataSchema, row.jsonbColumn);
const data = result.success ? result.output : null;
```

## TypeScript Conventions

### Type Inference over Explicit Types

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

### Generic Arguments for Inference

```typescript
// BAD - type annotation
const m: Map<string, User> = new Map();

// GOOD - generic argument
const m = new Map<string, User>();
```

### Exception: Discriminated Unions

Keep explicit types for discriminated unions where inference fails:

```typescript
// useState needs explicit type for union
const [state, setState] = useState<'idle' | 'loading' | 'error'>('idle');

// createContext needs explicit type
const Context = createContext<UserContextValue | null>(null);
```

### Prefer Switch over Object Maps

```typescript
// BAD - object map
const statusColors = {
	SUCCESS: 'green',
	FAILED: 'red',
	PENDING: 'yellow',
};
const color = statusColors[status];

// GOOD - switch with exhaustiveness over `const enum`
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

### No Non-null Assertions

```typescript
// BAD - non-null assertion
const user = users.find(u => u.id === id)!;

// GOOD - runtime validation
const user = users.find(u => u.id === id);
if (typeof user === 'undefined') throw new Error(`User not found: ${id}`);
```

### No Type Casts

```typescript
// BAD - type cast
const data = response as UserData;

// GOOD - validation
const result = v.safeParse(UserDataSchema, response);
if (!result.success) throw new Error('Invalid response');
const data = result.output;
```

### Destructuring over Array Indexes

```typescript
// BAD - array index
const first = arr[0];

// GOOD - destructuring
const [first] = arr;
```

## `noUncheckedIndexedAccess` Patterns

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

### Ternary string literal widening — annotate union types

TypeScript widens ternary results of string literals to `string`:

```typescript
// BAD - viewMode is string, not "list" | "detail"
const viewMode = isDetail ? 'detail' : 'list';

// GOOD - explicit union annotation (matches the "discriminated union exception" rule)
const viewMode = isDetail ? ('detail' as const) : ('list' as const);
```
