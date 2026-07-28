# Dialog Form Ownership

Keep the dialog or sheet shell free of form state. Render a state-owning form inside `Dialog.Content` or `Sheet.Content`, which mounts only while the shell is open.

```svelte
<!-- index.svelte -->
<!-- BAD: shell visibility and mutable form state are coupled. -->
<Dialog.Content><MyForm bind:values bind:open /></Dialog.Content>

<!-- GOOD: the shell owns visibility; the mounted form owns its state. -->
<Dialog.Root bind:open>
	<Dialog.Content>
		<MyForm {onSuccess} />
	</Dialog.Content>
</Dialog.Root>

<!-- form.svelte owns local input state and mutations -->
```

The parent owns `open`. The form calls `onSuccess`; the parent decides whether to close the shell.
