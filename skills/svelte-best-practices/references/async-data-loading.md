# Async Data Loading in Svelte

## Core Principle

**Separate data-fetching from presentation.** Use a `content.svelte` wrapper that runs queries and gates rendering, and a `form.svelte` that is a pure component receiving resolved props.

## Outer-Loader Pattern

```
index.svelte    → Shell (Dialog/Sheet)
content.svelte  → Outer loader — runs queries, gates rendering
form.svelte     → Inner form — pure, receives resolved props
```

## `content.svelte` — Outer Loader

Runs queries, checks if all `.data` is resolved, renders form or spinner.

```svelte
<script lang="ts" module>
	export interface ContentProps {
		onSuccess: () => void;
	}
</script>

<script lang="ts">
	import LoaderCircle from '@lucide/svelte/icons/loader-circle';
	import MyForm from './form.svelte';

	const { onSuccess }: ContentProps = $props();

	const fooQuery = createFooQuery();
</script>

{#if typeof fooQuery.data === 'undefined'}
	<div class="flex items-center justify-center p-4">
		<LoaderCircle class="animate-spin" />
	</div>
{:else}
	<MyForm {onSuccess} items={fooQuery.data} />
{/if}
```

## `form.svelte` — Pure Form

Accepts resolved data as props. Seeds mutable state from props where needed (e.g., edit forms).

```svelte
<script lang="ts" module>
	export interface FormProps {
		onSuccess: () => void;
		items: FooRow[];
		initialValue: string;
	}
</script>

<script lang="ts">
	const { onSuccess, items, initialValue }: FormProps = $props();

	// Seed mutable state from resolved prop
	// Safe — component only mounts when data is ready
	let selected = $state(initialValue);
</script>
```

## `index.svelte` — Shell

Imports `content.svelte` (not `form.svelte`). Extends `ContentProps`.

```svelte
<script lang="ts">
	import * as Dialog from '$lib/components/ui/dialog';
	import MyContent, { type ContentProps } from './content.svelte';

	interface Props extends ContentProps {
		open: boolean;
	}

	let { open = $bindable(false), onSuccess }: Props = $props();
</script>

<Dialog.Root bind:open>
	<Dialog.Content>
		<Dialog.Header>
			<Dialog.Title>Title</Dialog.Title>
			<Dialog.Description>Description</Dialog.Description>
		</Dialog.Header>
		<MyContent {onSuccess} />
	</Dialog.Content>
</Dialog.Root>
```

## Why

- **Conditional state mounting**: `$state(initialValue)` seeds correctly because the form only mounts once data is available
- **Sans-I/O testability**: `form.svelte` is a pure component that accepts resolved data as props — no DB mocking needed
- **Loading UX**: spinner shown while queries resolve; form never renders in a partial state

## Key Points

- Gate on `typeof query.data !== 'undefined'` (not truthiness — `null` is a valid resolved value)
- `content.svelte` exports `ContentProps`; `index.svelte` extends it
- `form.svelte` exports `FormProps`; `content.svelte` imports it for type-safe prop forwarding
- Mutations stay in `form.svelte` — only **queries** move to `content.svelte`
- Combine with [Conditional State](conditional-state.md) for the full feature module pattern
