---
name: zod-best-practices
description: Opinionated Zod v4 schema conventions for top-level formats, nullability, discriminated unions, and parse policy. Use when defining validation schemas or parsing untrusted data.
---

# Zod Best Practices

Opinionated conventions for Zod (^4.x). This is not API documentation (see the footer for that); every section is a rule with a rationale.

Import once at the top; the `z` export carries every schema constructor and the top-level format validators:

```typescript
import { z } from 'zod';
```

## Schema as Single Source of Truth

Define the schema once and infer the type from it. Export both under the same name so callers import one symbol. Never hand-write a type that a schema already proves.

```typescript
// BAD - hand-written type drifts from the schema
const UserSchema = z.object({ id: z.string(), name: z.string() });
interface User {
	id: string;
	name: string;
}

// GOOD - schema and type share one name, one definition
export const User = z.object({ id: z.uuid(), name: z.string() });
export type User = z.infer<typeof User>;
```

Reach for `z.input<typeof User>` only when transforms or defaults make the input and output types diverge.

## Top-Level Format Validators

Reach for the top-level format functions for string formats. They read cleanly and tree-shake to only the formats you use.

```typescript
const schema = z.object({
	email: z.email(),
	id: z.uuid(),
	website: z.url(),
});
```

## Nullability Semantics

Pick the wrapper that matches the field's wire semantics, not whichever compiles:

```typescript
// Field always present, value may be null
created_at: z.string().nullable(),

// Field may be absent, but if present, has a value
source: z.string().optional(),

// Field may be absent OR null
category: z.string().nullish(),
```

Prefer `.nullable()` over `.optional()` for wire protocols: an explicit `null` distinguishes "known to be empty" from "field forgotten", which a missing key cannot.

`.default(x)` makes the input optional but the output non-optional; the default must be assignable to the **output** type and short-circuits without parsing. Use `.prefault(x)` when the fallback must instead run _through_ validation as raw input.

## Discriminated Unions Use `z.discriminatedUnion`

`z.union` over literal-tagged objects tries every branch sequentially and reports issues from all of them. `z.discriminatedUnion` dispatches on the discriminant key: O(1) lookup, type-safe on the tag, and issues point at the actual failing branch. Reserve `z.union` for genuinely un-tagged alternatives (e.g. `z.union([z.string(), z.number()])`).

```typescript
// BAD - union of literal-tagged objects
const Response = z.union([
	z.object({ status: z.literal('success'), data: z.string() }),
	z.object({ status: z.literal('failed'), error: z.string() }),
]);

// GOOD - discriminated union dispatches on the tag
const Response = z.discriminatedUnion('status', [
	z.object({ status: z.literal('success'), data: z.string() }),
	z.object({ status: z.literal('failed'), error: z.string() }),
]);
```

## Object Strictness is Explicit

`z.object` strips unknown keys by default. State any stricter or looser intent with the dedicated constructors rather than a chained modifier.

```typescript
// Strips unknown keys (default) - use for lenient/forward-compatible payloads
z.object({ name: z.string() });

// Rejects unknown keys - use for closed contracts where extras signal a bug
z.strictObject({ name: z.string() });

// Preserves unknown keys - use only when passing through opaque data
z.looseObject({ name: z.string() });
```

## Parse Policy

`.parse` throws; use it for trusted sources where invalid data is a programming error. `.safeParse` returns a result; use it for untrusted input (user forms, JSONB columns) where invalid data is an expected case to handle.

```typescript
// Trusted source (e.g. network data after auth) - let it throw
const user = User.parse(data);

// Untrusted input - handle the failure path
const result = KpiData.safeParse(row.jsonbColumn);
const kpi = result.success ? result.data : null;
```

When you need to surface the errors, use the top-level helpers:

```typescript
if (!result.success) {
	z.treeifyError(result.error); // nested tree, for field-level UI
	z.prettifyError(result.error); // human-readable string, for logs
}
```

## Custom Validation via `.check()`

For simple predicates use `.refine()`. For multi-issue or cross-field logic use `.check()`, which pushes issues directly onto the context. Set messages through the unified `error` option.

```typescript
// Simple predicate
const Password = z.string().refine(s => s.length >= 8, { error: 'Too short' });

// Multi-issue logic - push onto the context
const UniqueTags = z.array(z.string()).check(ctx => {
	if (ctx.value.length !== new Set(ctx.value).size) {
		ctx.issues.push({ code: 'custom', message: 'Tags must be unique', input: ctx.value });
	}
});
```

## Documentation

- If available, use Context7 (Library ID: `/colinhacks/zod`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `colinhacks/zod`) for implementation details.
