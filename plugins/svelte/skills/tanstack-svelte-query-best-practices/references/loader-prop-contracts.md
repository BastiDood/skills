# Loader Prop Contracts

Export the loader props as the public component contract. Extend one shared capability contract for callbacks used by both the loader and resolved component.

```svelte
<!-- BAD: the resolved form accepts transport state. -->
<ItemsForm items={query.data} isPending={query.isPending} />

<!-- GOOD: public loader identity becomes resolved data in the inner contract. -->
<!-- content.svelte -->
<script lang="ts" module>
	export interface ContentProps extends ItemActions {
		categoryId: string;
	}
</script>
```

```svelte
<!-- form.svelte -->
<script lang="ts" module>
	interface FormProps extends ItemActions {
		items: Item[];
	}
</script>
```

The loader contract owns query identity. The inner contract replaces query state with resolved data and retains only caller capabilities it uses. Do not expose the query result through either prop contract.
