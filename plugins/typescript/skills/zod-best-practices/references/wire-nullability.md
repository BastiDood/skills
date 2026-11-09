# Wire Nullability

Choose the wrapper that matches a field's wire semantics, not whichever one compiles.

```typescript
const Record = z.object({
	// Always present with a value that can be `null`.
	created_at: z.string().nullable(),
	// Can be absent but has a value when present.
	source: z.string().optional(),
	// Can be absent or `null`.
	category: z.string().nullish(),
});
```

Prefer `.nullable()` over `.optional()` for wire protocols when explicit `null` means "known empty" and a missing key means "not supplied."
