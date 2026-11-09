# Affirmative Conditions

Name boolean concepts after the valid or permitted state. Test that state directly. A reader must not have to negate a negative concept before understanding whether a branch runs.

```typescript
// BAD: a reader must resolve a double negative.
if (!request.isInvalid) execute(request);

// GOOD: test the permitted state directly.
if (request.isValid) execute(request);
```

Do not create an inverse name only to avoid one necessary negation. The affirmative underlying concept keeps `if (!request.isAuthenticated)` clear. The target is semantic clarity, not the mechanical removal of every `!`.

During review, rewrite conditions that require mental truth tables or repeated negation.
