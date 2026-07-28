# Explicit Rendering

Use an explicit condition when a rendered value can be absent, empty, zero, or otherwise ambiguous. Do not make a value's truthiness decide whether it renders.

```svelte
<!-- BAD: Truthiness Hides Valid Values -->

<script lang="ts">
	let activeUsers = $state(0);
</script>

{#if activeUsers}
	<p>{activeUsers} active users</p>
{/if}
```

```svelte
<!-- GOOD: The Rendering Rule Names the Actual Condition -->

<script lang="ts">
	let activeUsers = $state(0);
</script>

{#if activeUsers >= 0}
	<p>{activeUsers} active users</p>
{/if}
```

Use an explicit `typeof value !== 'undefined'`, `value === null`, length, status, or domain comparison that matches the intended UI state.
