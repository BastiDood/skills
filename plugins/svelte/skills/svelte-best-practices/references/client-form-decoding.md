# Client Form Decoding

For an SPA mutation form, decode `FormData` with `decode-formdata` before validation. Declare every numeric, date, and array field so the schema receives the intended runtime types.

```svelte
<script lang="ts">
	import { decode } from 'decode-formdata';

	// BAD: conversion alone does not validate untrusted form values.
	function submitUnchecked(form: HTMLFormElement) {
		mutation.mutate(decode(new FormData(form)));
	}

	// GOOD: isolate representation decoding before schema validation.
	function decodeCreateItemForm(form: HTMLFormElement) {
		return decode(new FormData(form), {
			numbers: ['amount', 'quantity'],
			dates: ['datedAt'],
			arrays: ['tags'],
		});
	}
</script>
```

Decoding converts representations; it does not establish trust. Validate the returned value before invoking the mutation.
