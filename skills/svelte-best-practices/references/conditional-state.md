# Conditional State in Svelte

## Core Principle

**Mount `$state` only when the component renders.** Wrap stateful components inside conditional containers (Dialog.Content, Sheet.Content, `{#if}`) so state is lazily initialized on mount and automatically destroyed on close. No stale state, no manual cleanup.

## Why

- `$state` initializes when the component mounts
- `Dialog.Content` / `Sheet.Content` only mounts when `open=true`
- When the dialog closes, the content unmounts → all `$state` is destroyed
- When it reopens, fresh state is created
- No `$effect` needed to reset state

## Pattern: Feature Module (Dialog/Sheet)

Split into two files: shell (`index.svelte`) and form (`form.svelte`).

### `form.svelte` — Owns State

Mounted inside Content (only when open). Owns `$state`, mutations, validation. State is lazily initialized on mount, destroyed on close.

```svelte
<script lang="ts" module>
	export interface FormProps {
		onSuccess: () => void;
	}
</script>

<script lang="ts">
	import * as v from 'valibot';
	import { decode } from 'decode-formdata';

	const { onSuccess }: FormProps = $props();

	// These are lazily initialized when the dialog opens
	let name = $state('');
	let amount = $state(0);

	// When dialog closes, this component unmounts → state is destroyed
	// When dialog reopens, fresh state is created
</script>

<form onsubmit={event => { /* ... */ }}>
	<input bind:value={name} />
	<input bind:value={amount} type="number" />
	<button type="submit">Save</button>
</form>
```

### `index.svelte` — Shell

Wraps Dialog/Sheet. Contains NO form `$state`.

```svelte
<script lang="ts">
	import * as Dialog from '$lib/components/ui/dialog';
	import MyForm, { type FormProps } from './form.svelte';

	interface Props extends FormProps {
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
		<MyForm {onSuccess} />
	</Dialog.Content>
</Dialog.Root>
```

### Parent Usage

```svelte
<script lang="ts">
	let settingsOpen = $state(false);
</script>

<SettingsDialog
	bind:open={settingsOpen}
	onSuccess={() => {
		settingsOpen = false;
	}}
/>
```

## Why Not Reset State in `$effect`?

```svelte
<script lang="ts">
	// BAD — manual reset, easy to miss fields, stale state bugs
	let { open }: Props = $props();
	let name = $state('');
	let email = $state('');

	$effect(() => {
		if (open) {
			name = '';
			email = '';
		}
	});
</script>

<!-- GOOD — Dialog.Content handles it automatically -->
<!-- form.svelte only mounts when dialog is open -->
```

## Beyond Dialogs

This pattern applies to any conditionally visible UI:

- **Tabs**: Each tab panel can be a separate component
- **Accordions**: Content mounts when expanded, unmounts when collapsed
- **Wizards/Steps**: Each step is a component with its own `$state`
- **Drawers/Sheets**: Same as dialogs

```svelte
<!-- Tab panels with lazy state -->
{#if activeTab === 'settings'}
	<SettingsPanel />
{:else if activeTab === 'profile'}
	<ProfilePanel />
{/if}
```

## Key Points

- `Dialog.Content` / `Sheet.Content` only mounts when `open=true` → form state is lazy
- Parent controls open state; `onSuccess` handles close-on-success
- `form.svelte` exports `FormProps` from a `module` script block so the shell can extend it
- Follow [Form Handling](./form-handling.md) for submit patterns (submitter disabling, Valibot, decode-formdata)
