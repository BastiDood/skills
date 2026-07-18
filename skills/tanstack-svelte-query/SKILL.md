---
name: tanstack-svelte-query
description: Data fetching with `TanStack Svelte Query` using runes-based reactivity. Use when fetching data, managing cache, or implementing mutations in Svelte.
---

# TanStack Svelte Query

Data fetching patterns with `TanStack Query` for Svelte 5, using runes-based reactivity.

## Documentation

- If available, use Context7 (Library ID: `/tanstack/query`) to fetch the latest documentation.
- If available, use DeepWiki (GitHub Repository: `TanStack/query`) for implementation details.

## Query Pattern

```svelte
<script lang="ts">
	import { createQuery, skipToken } from '@tanstack/svelte-query';

	const { itemId }: Props = $props();

	const query = createQuery({
		queryKey: ['items', itemId],
		queryFn: itemId
			? ({ queryKey: [, id] }) => fetchItem(id)
			: skipToken,
	});
</script>

{#if query.isPending}
	<Spinner />
{:else if query.isError}
	<ErrorBanner error={query.error} />
{:else if typeof query.data !== 'undefined'}
	<ItemDisplay data={query.data} />
{/if}
```

## Query Key Factory

```typescript
export const itemQueryKeys = {
	all: ['items'] as const,
	list: (filters?: ItemFilters) =>
		filters ? (['items', 'list', filters] as const) : (['items', 'list'] as const),
	detail: (id: string) => ['items', 'detail', id] as const,
};
```

## Outer-Loader Pattern

Separate data-fetching from presentation using `content.svelte` as a query gate:

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

## Mutation Pattern

```svelte
<script lang="ts">
	import { createMutation } from '@tanstack/svelte-query';

	const mutation = createMutation({
		mutationFn: (data: CreateItemRequest) => createItem(data),
		onSuccess: (data, variables, onMutateResult, context) => {
			context.client.invalidateQueries({ queryKey: itemQueryKeys.all });
		},
	});
</script>

<form
	onsubmit={event => {
		event.preventDefault();
		const { currentTarget, submitter } = event;
		// ... parse FormData with decode-formdata + valibot
		mutation.mutate(parsed, {
			onSuccess: () => onSuccess(),
			onSettled: () => {
				submitter.disabled = false;
			},
		});
	}}
>
```

### Per-Call vs Hook-Level Callbacks

- **Hook-level** (`onSuccess` in `createMutation`): Cache invalidation, global side effects
- **Per-call** (`onSuccess` in `mutation.mutate`): Component-specific logic (close dialog, reset form)

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

## Reactive Queries with Runes

Use `$derived` for reactive query keys:

```svelte
<script lang="ts">
	let { categoryId }: Props = $props();

	// Query key is reactive — query refetches when categoryId changes
	const query = $derived(createQuery({
		queryKey: ['items', 'list', categoryId],
		async queryFn({ queryKey: [, , id] }) {
			return await fetchItemsByCategory(id);
		},
	}));
</script>
```

## Key Points

- Use `createQuery` and `createMutation` (not `useQuery`/`useMutation` — those are React)
- Query results are reactive runes — access `.data`, `.isPending`, etc. directly
- Use `mutate`, never `mutateAsync` — let per-call callbacks handle outcomes
- Combine with the outer-loader pattern for data-dependent forms
