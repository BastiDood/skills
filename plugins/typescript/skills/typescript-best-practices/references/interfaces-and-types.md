# Interfaces and Types

Default handwritten object contracts to `interface`. When composing object shapes, prefer `extends` over `&`: interface relationships are cached, while intersections recursively merge their constituents and require more work during type checking. At scale, `extends` is far cheaper, and it is sufficient for most object composition.

```typescript
interface Identified {
	id: string;
}

interface Named {
	name: string;
}

// BAD: an intersection makes the compiler merge every constituent.
type IntersectedUserRecord = Identified & Named & { createdAt: Date };

// GOOD: extends creates one flat, cacheable object relationship.
interface ExtendedUserRecord extends Identified, Named {
	createdAt: Date;
}
```

Use `type` where `interface` cannot express the contract naturally: structured unions, mapped types, conditional types, primitives, and schema-inferred outputs. Use `const enum` for named closed primitive values.

Do not replace a natural interface with an intersection solely to avoid `extends`.

For an exact subset of an existing contract, use `Pick` instead of copying properties or creating an empty interface that only extends the subset. Add an interface when it introduces a meaningful new object contract with its own fields.

```typescript
// BAD: a copied subset can drift from AvatarProps.
interface Props {
	title: string;
	size: number;
}

// GOOD: preserve the source property's optional and readonly modifiers too.
type Props = Pick<AvatarProps, 'title' | 'size'>;
```
