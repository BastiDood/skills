# Dialog Form Ownership

Keep the dialog or sheet shell responsible for visibility. Put mutable drafts and submission work in the form, and decide whether the draft should survive closing before choosing its mount lifetime.

```svelte
<!-- BAD: shell visibility and mutable form state are coupled. -->
<Dialog.Content><MyForm bind:values bind:open /></Dialog.Content>

<!-- GOOD: the shell owns visibility; the form owns its draft and submission. -->
<Dialog.Root bind:open>
	<Dialog.Content>
		<MyForm {onSuccess} />
	</Dialog.Content>
</Dialog.Root>
```

The parent owns `open`. The form calls `onSuccess`; the parent decides whether to close the shell.

Inspect the installed primitive and wrapper before relying on close to reset a draft. Ordinary presence handling may delay destruction until an exit transition finishes; `forceMount` or a custom child snippet can keep content mounted. If closing must discard the draft, give the form an actual unmount or an intentional identity reset while preserving required transition and focus behavior. If drafts must survive closing, retain them in their chosen owner. Changing `open` alone does not prove either lifetime.
