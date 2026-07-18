---
name: tanstack-svelte-query-best-practices
description: Opinionated TanStack Svelte Query conventions covering the Outer-Loader pattern and runes-based reactive queries. Use when fetching data or implementing mutations in Svelte.
---

# TanStack Svelte Query Best Practices

Opinionated conventions for TanStack Query in Svelte 5. API documentation lives elsewhere (see the footer); every section here is a rule.

## Outer-Loader Pattern

Separate data fetching from presentation using `content.svelte` as a query gate. The inner component receives resolved data and never deals with query states.

```svelte
<!-- content.svelte — runs queries, gates rendering -->
<script lang="ts" module>
	export interface ContentProps {
		onSuccess: () => void;
	}
</script>

<script lang="ts">
	import { createQuery } from '@tanstack/svelte-query';
	import LoaderCircle from '@lucide/svelte/icons/loader-circle';
	import MyForm from './form.svelte';

	const { onSuccess }: ContentProps = $props();

	const query = createQuery({
		queryKey: ['items'],
		queryFn: fetchItems,
	});
</script>

{#if typeof query.data !== 'undefined'}
	<MyForm {onSuccess} items={query.data} />
{:else}
	<div class="flex items-center justify-center p-4">
		<LoaderCircle class="animate-spin" />
	</div>
{/if}
```

Gate on `typeof query.data !== 'undefined'` — not truthiness, since `null` is a valid resolved value.

## Query Key Factory

Centralize query keys in one factory per entity so query definitions and invalidation targets can never drift apart:

```typescript
export const itemQueryKeys = {
	all: ['items'] as const,
	list: (filters?: ItemFilters) =>
		filters ? (['items', 'list', filters] as const) : (['items', 'list'] as const),
	detail: (id: string) => ['items', 'detail', id] as const,
};
```

## Reactive Queries via `$derived`

`createQuery` evaluates its options once. When the query key depends on reactive state, wrap the call in `$derived` — otherwise the query is created with the initial value and never re-fires.

```svelte
<script lang="ts">
	let { categoryId }: Props = $props();

	// BAD - key captured once, query never reacts to categoryId changes
	const stale = createQuery({
		queryKey: ['items', 'list', categoryId],
		queryFn: () => fetchItemsByCategory(categoryId),
	});

	// GOOD - query recreated when categoryId changes
	const query = $derived(createQuery({
		queryKey: ['items', 'list', categoryId],
		async queryFn({ queryKey: [, , id] }) {
			return await fetchItemsByCategory(id);
		},
	}));
</script>
```

## Hook-Level vs Per-Call Callbacks

Split mutation callbacks by concern: cache policy belongs to the mutation definition, component behavior belongs to the call site.

- **Hook-level** (`onSuccess` in `createMutation`): cache invalidation, global side effects. Use `context.client` (TanStack Query >= 5.89); never reach for a client from component scope.
- **Per-call** (`onSuccess` in `mutation.mutate`): component-specific logic (close dialog, reset form, re-enable submitter).

```svelte
<script lang="ts">
	const mutation = createMutation({
		mutationFn: updateItem,
		// Hook-level: always invalidate cache
		onSuccess(data, variables, onMutateResult, context) {
			context.client.invalidateQueries({ queryKey: itemQueryKeys.all });
		},
	});

	function handleSubmit(data: UpdateItemRequest) {
		mutation.mutate(data, {
			// Per-call: close this specific dialog
			onSuccess,
		});
	}
</script>
```

## `mutate` over `mutateAsync`

Use `mutate` and let per-call callbacks handle outcomes. `mutateAsync` forces manual `try`/`catch` at every call site and leaves dangling promises when the result is ignored.

```typescript
// BAD - manual outcome plumbing
try {
	await mutation.mutateAsync(data);
	closeDialog();
} finally {
	submitter.disabled = false;
}

// GOOD - callbacks own the outcomes
mutation.mutate(data, {
	onSuccess: () => closeDialog(),
	onSettled: () => {
		submitter.disabled = false;
	},
});
```

## Documentation

- If available, use Context7 (Library ID: `/tanstack/query`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `TanStack/query`) for implementation details.
