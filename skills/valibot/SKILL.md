---
name: valibot
description: Schema validation with Valibot. Use when validating data, parsing API responses, defining schemas, or working with type-safe data transformations.
---

# Valibot - Schema Validation

Lightweight, type-safe schema validation library.

**Libraries:** valibot (^1.x)

## Documentation

- If available, use `context7` (Library ID: `fabian-hiller-valibot`) to fetch the latest documentation.
- If available, use `deepwiki` (GitHub Repository: `fabian-hiller/valibot`) for implementation details.

## Import Pattern

```typescript
import * as v from 'valibot';
```

## Schema Builders

```typescript
// Objects
const User = v.object({ id: v.string(), name: v.string(), age: v.optional(v.number()) });

// Records (maps)
const Snapshot = v.record(v.string(), v.nullable(Position));

// Nullable vs Optional vs Nullish
v.nullable(v.number()); // number | null
v.optional(v.number()); // number | undefined
v.nullish(v.number()); // number | null | undefined

// Unions
const Status = v.union([v.literal('idle'), v.literal('loading'), v.literal('error')]);

// Pipelines
const Timestamp = v.pipe(
	v.number(),
	v.transform(n => new Date(n)),
);
const Email = v.pipe(v.string(), v.email());
```

## Type Inference

Export both schema and type using the same name:

```typescript
export const User = v.object({ id: v.string(), name: v.string() });
export type User = v.InferOutput<typeof User>;
```

## Parsing

```typescript
// Throwing parse (trusted sources — network data after auth)
const user = v.parse(UserSchema, data);

// Safe parse (untrusted input — user forms, JSONB columns)
const result = v.safeParse(UserSchema, data);
if (result.success) {
	const user = result.output;
} else {
	console.error('Invalid:', result.issues);
}
```

## Best Practices

1. **Import as namespace**: `import * as v from 'valibot'`
2. **Export both schema and type**: Use same name for convenience
3. **Use `v.parse()` for trusted sources** (network data after auth)
4. **Use `v.safeParse()` for untrusted input** (user forms, JSONB)
5. **Prefer `nullable` over `optional`** for wire protocols (explicit `null` vs missing)
6. **Use `v.nullish()`** instead of `v.optional(v.nullable(...))`
7. **Use pipelines for transforms** (parsing dates, normalization)
