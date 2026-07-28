# Resolved Form Props

Make a form that receives resolved data a pure component. Seed mutable edit state from props only when the parent mounts the form after all required values resolve.

```svelte
<!-- BAD: a resolved form starts its own request just to obtain its inputs. -->
<EditForm itemId={itemId} />

<!-- GOOD: the parent resolves data; the form owns only its local edits. -->
<script lang="ts" module>
	export interface FormProps {
		onSuccess: () => void;
		items: FooRow[];
		initialValue: string;
	}
</script>

<script lang="ts">
	const { onSuccess, items, initialValue }: FormProps = $props();
	let selected = $state(initialValue);
</script>
```

Keep I/O outside the resolved-prop component unless the component owns a user-triggered mutation. Test project-owned behavior with ordinary resolved props instead of mocking the upstream data source.
