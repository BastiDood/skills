# Submitter Identity

Treat `event.submitter === null` as valid for keyboard and programmatic submission. Do not cast the submit event.

```typescript
// BAD: button identity is not guaranteed by the browser.
const action = (event.submitter as HTMLButtonElement).value;

// GOOD: require identity only when distinct operations need it.
if (!(event.submitter instanceof HTMLButtonElement)) {
	formError = 'Choose an action.';
	return;
}
const action = event.submitter.value;
```

- If every submission uses the same action, proceed without a submitter.
- If the selected button determines the action, validate the submitter at that decision point.
- When the action cannot be identified, show a normal validation error and do not invent a default action.

The selected browser button is not an authorization boundary.
