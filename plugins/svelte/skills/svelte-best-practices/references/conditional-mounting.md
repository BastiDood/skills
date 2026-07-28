# Conditional Mounting

Mount stateful UI only while it renders. Mounting initializes its `$state`; unmounting destroys it. This provides fresh state on the next mount without manual cleanup.

```svelte
<!-- BAD: a hidden child keeps state alive and needs a separate reset policy. -->
<SettingsPanel hidden={activeTab !== 'settings'} />

<!-- GOOD: mounting defines the visible panel's state lifetime. -->
{#if activeTab === 'settings'}
	<SettingsPanel />
{:else if activeTab === 'profile'}
	<ProfilePanel />
{/if}
```

Use the same rule for tabs, accordions, drawers, sheets, wizards, and other conditionally visible UI.

Use conditional mounting only when hidden UI does not need to preserve its local state. If state must survive hiding or closing, preserve it in the actual owner and pass it back into the mounted component.
