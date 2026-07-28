# Form Action Defaults

Use a SvelteKit form action with `method="POST"` and `use:enhance` by default. Use `onsubmit` with a client mutation only when no form-action lifecycle exists.

```svelte
<!-- BAD: bypasses an available SvelteKit action -->
<form onsubmit={handler}></form>

<!-- GOOD: progressive form lifecycle -->
<form method="POST" use:enhance={submitFunction}></form>
```

Use absolute `action` and `formaction` paths such as `/members?/promote`. Named actions still validate request data on the server.
