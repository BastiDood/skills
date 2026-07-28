# State Ownership

Place state in the smallest component subtree that reads and changes it. Lift state only when multiple sibling consumers need the same source of truth.

```svelte
<!-- BAD: A Shell Owns a Child's Private State -->

<!-- ProfilePage.svelte -->
<script lang="ts">
	let displayName = $state('');
</script>

<ProfileEditor bind:displayName />
```

```svelte
<!-- GOOD: The Stateful Child Owns Its Private State -->

<!-- ProfilePage.svelte -->
<ProfileEditor />
```

```svelte
<!-- GOOD: The Stateful Child Owns Its Private State -->

<!-- ProfileEditor.svelte -->
<script lang="ts">
	let displayName = $state('');
</script>
```

Do not lift state preemptively to make a parent look in control. Local state makes ownership explicit and lets component identity control its lifetime.
