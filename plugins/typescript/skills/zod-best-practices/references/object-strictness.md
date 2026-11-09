# Object Strictness

`z.object` strips unknown keys by default. State stricter or looser intent with a dedicated constructor, not a chained modifier.

```typescript
// Strips unknown keys. Use for lenient forward-compatible payloads.
z.object({ name: z.string() });

// Rejects extras. Use for closed contracts where extras signal a bug.
z.strictObject({ name: z.string() });

// Preserves extras. Use only for opaque pass-through data.
z.looseObject({ name: z.string() });
```

Pick the constructor from the contract for unknown fields.
