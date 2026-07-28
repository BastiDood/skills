# Derived State

Derive values with `$derived`; do not synchronize one piece of reactive state into another with `$effect`.

```svelte
<!-- BAD: Maintain a Second Writable Copy -->

<script lang="ts">
	let firstName = $state('Ada');
	let lastName = $state('Lovelace');
	let fullName = $state('');

	$effect(() => {
		fullName = `${firstName} ${lastName}`;
	});
</script>
```

```svelte
<!-- GOOD: Keep One Source of Truth -->

<script lang="ts">
	let firstName = $state('Ada');
	let lastName = $state('Lovelace');
	const fullName = $derived(`${firstName} ${lastName}`);
</script>
```
