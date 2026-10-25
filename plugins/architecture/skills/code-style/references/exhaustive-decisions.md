# Exhaustive Decisions

Handle every member of a finite state space. Make new or unknown members fail visibly instead of falling through to an unrelated default.

```typescript
const enum Status {
	Active = 'active',
	Paused = 'paused',
	Stopped = 'stopped',
}

// BAD: every non-active state inherits gray.
function statusColorWithFallback(status: Status) {
	if (status === Status.Active) return 'green';
	return 'gray';
}

// GOOD: each state has an explicit case.
function statusColor(status: Status) {
	switch (status) {
		case Status.Active:
			return 'green';
		case Status.Paused:
			return 'yellow';
		case Status.Stopped:
			return 'gray';
	}
}
```

Enforce completeness with the target language's exhaustive type checker or linter. Keep closed decisions limited to real cases instead of adding synthetic unreachable branches or assertion helpers. Validate external values at their input boundary.
