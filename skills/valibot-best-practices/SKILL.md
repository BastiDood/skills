---
name: valibot-best-practices
description: Opinionated Valibot schema conventions for nullability, discriminated unions, and parse policy. Use when defining validation schemas or parsing untrusted data.
---

# Valibot Best Practices

Opinionated conventions for `valibot` (^1.x). This is not API documentation (see the footer for that); every section is a rule with a rationale.

Always import as a namespace: `import * as v from 'valibot';`.

## Schema as Single Source of Truth

Define the schema once and infer the type from it. Export both under the same name so callers import one symbol. Never hand-write a type that a schema already proves.

```typescript
// BAD - hand-written type drifts from the schema
const UserSchema = v.object({ id: v.string(), name: v.string() });
interface User {
	id: string;
	name: string;
}

// GOOD - schema and type share one name, one definition
export const User = v.object({ id: v.string(), name: v.string() });
export type User = v.InferOutput<typeof User>;
```

## Nullability Semantics

Pick the wrapper that matches the field's wire semantics, not whichever compiles:

```typescript
// Field always present, value may be null
created_at: v.nullable(v.string()),

// Field may be absent, but if present, has a value
source: v.optional(v.string()),

// Field may be absent OR null
category: v.nullish(v.string()),
```

Prefer `nullable` over `optional` for wire protocols: an explicit `null` distinguishes "known to be empty" from "field forgotten", which a missing key cannot.

Never nest the wrappers to express nullish:

```typescript
// BAD - verbose and redundant
filename: v.optional(v.nullable(v.string())),

// GOOD - concise equivalent
filename: v.nullish(v.string()),
```

## Discriminated Unions Use `v.variant`

`v.union` over literal-tagged objects tries every branch and reports issues from all of them. `v.variant` dispatches on the discriminant key: faster, type-safe on the tag, and issues point at the actual failing branch. Reserve `v.union` for genuinely un-discriminated unions (e.g. `v.union([v.string(), v.number()])`).

```typescript
// BAD - union of literal-tagged objects
const Response = v.union([
	v.object({ status: v.literal(StatusEnum.SUCCESS), data: v.string() }),
	v.object({ status: v.literal(StatusEnum.FAILED), error: v.string() }),
]);

// GOOD - variant dispatches on the discriminant
const Response = v.variant('status', [
	v.object({ status: v.literal(StatusEnum.SUCCESS), data: v.string() }),
	v.object({ status: v.literal(StatusEnum.FAILED), error: v.string() }),
]);
```

## Parse Policy

`v.parse` throws; use it for trusted sources where invalid data is a programming error. `v.safeParse` returns a result; use it for untrusted input (user forms, JSONB columns) where invalid data is an expected case to handle.

```typescript
// Trusted source (e.g. network data after auth) - let it throw
const user = v.parse(User, data);

// Untrusted input - handle the failure path
const result = v.safeParse(KpiData, row.jsonbColumn);
const kpi = result.success ? result.output : null;
```

## Pipelines for Transforms

Parse and normalize in the schema so consumers receive the final shape, not raw wire values:

```typescript
const Timestamp = v.pipe(
	v.number(),
	v.transform(n => new Date(n)),
);
```

## Documentation

- If available, use Context7 (Library ID: `/open-circle/valibot`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `open-circle/valibot`) for implementation details.
