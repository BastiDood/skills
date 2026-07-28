# Cache Identity

Every input that can change a query response belongs in its query key. Centralize keys in one factory per entity so query definitions and invalidation targets cannot drift apart.

```typescript
// BAD: distinct filters reuse one cache entry.
const queryKey = ['items', 'list'] as const;

// GOOD: every response-changing input participates in identity.
export const itemQueryKeys = {
	all: ['items'] as const,
	list: (filters?: ItemFilters) =>
		typeof filters === 'undefined'
			? (['items', 'list'] as const)
			: (['items', 'list', filters] as const),
	detail: (id: string | undefined) => ['items', 'detail', id] as const,
};
```

Include filters, pagination state, locale, tenant, and other response-changing inputs. Keep values used only for client-side rendering outside the key.

Use readonly literal tuples and one hierarchy so broad invalidation can target an entity prefix while detailed invalidation targets one record. Review the cache identity whenever a request gains a response-changing argument.
