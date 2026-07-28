# Event-Owned Work

Event handling is event-based, not data-reactive. Perform work caused by a user action in the event handler that receives the action. Effects synchronize reactive data with external systems; they do not handle events.

Do not toggle state and use `$effect` to perform navigation, analytics, or a mutation indirectly.

```svelte
<!-- BAD: React to an Event Indirectly -->

<script lang="ts">
	let submitted = $state(false);

	$effect(() => {
		if (submitted) {
			sendAnalytics('form_submit');
			goto('/success');
		}
	});

	function handleSubmit() {
		submitted = true;
	}
</script>
```

```svelte
<!-- GOOD: The Event Owns Its Consequences -->

<script lang="ts">
	function handleSubmit() {
		sendAnalytics('form_submit');
		goto('/success');
	}
</script>
```
