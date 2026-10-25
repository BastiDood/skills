# Exhaustive Decisions

Use a `switch` when every member of a closed enum or structured union has distinct behavior. Enforce completeness with type-aware exhaustiveness checks. Do not add synthetic `default` branches, `never` annotations, `satisfies never`, or assertion helpers to prove a closed switch exhaustive.

```typescript
// BAD: Hide Missing Enum Members Behind a Default
const enum Status {
	Success = 'success',
	Failed = 'failed',
	Pending = 'pending',
}

function getStatusColor(status: Status) {
	switch (status) {
		case Status.Success:
			return 'green';
		case Status.Failed:
			return 'red';
		default:
			return 'gray';
	}
}
```

The fallback silently accepts `Status.Pending` and any future member.

```typescript
// GOOD: Handle Each Member Explicitly
const enum Status {
	Success = 'success',
	Failed = 'failed',
	Pending = 'pending',
}

function getStatusColor(status: Status) {
	switch (status) {
		case Status.Success:
			return 'green';
		case Status.Failed:
			return 'red';
		case Status.Pending:
			return 'yellow';
	}
}
```

Configure the checker or linter to report missing cases when the enum or union gains a member. Validate untrusted values before they enter the closed domain; do not add unreachable branches to compensate for an unchecked input boundary.

Use `satisfies Record<Enum, Value>` only for static data that has one value for every member of a closed enum.

```typescript
const statusColors = {
	[Status.Success]: 'green',
	[Status.Failed]: 'red',
	[Status.Pending]: 'yellow',
} satisfies Record<Status, string>;
```
