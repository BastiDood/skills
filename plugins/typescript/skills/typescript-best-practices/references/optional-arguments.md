# Optional Arguments and Properties

Omit an optional argument when omission expresses the intent. Pass `void 0` when an API requires an explicit undefined argument, including a positional slot before a later argument.

```typescript
// BAD: explicit undefined when omission is supported.
reset(undefined);

// GOOD: omit the optional argument.
reset();

// GOOD: the API requires an argument at this position.
resetAt(index, void 0);
```

An absent object property and a present property whose value is `undefined` are different states. Preserve the API's omission semantics so the code remains compatible with enabling `exactOptionalPropertyTypes` later. Do not change the compiler option as part of a local cleanup.

```typescript
interface Identified {
	id: string;
}

interface UserUpdate extends Identified {
	displayName?: string;
}

// BAD: materializes a field even when this update must leave it unchanged.
const update = { id, displayName: typeof name === 'undefined' ? void 0 : name };

// BAD: rebuilds the common object to select its optional shape.
const update = typeof name === 'undefined' ? { id } : { id, displayName: name };
```

```typescript
// GOOD: construct one locally owned update, then insert a present field.
const update: UserUpdate = { id };
if (typeof name !== 'undefined') update.displayName = name;
saveUser(update);
```

The extended interface reuses the base identity contract and declares the optional attachment. The annotation supplies that field contract where the initial object cannot infer it. For an existing object, copy once before conditional insertion when its ownership prohibits mutation. Avoid empty-object spread branches for the same decision. Keep simple scalar ternaries for selecting values. If an API deliberately distinguishes a present `undefined` from omission, retain that explicit contract instead of silently changing it.
