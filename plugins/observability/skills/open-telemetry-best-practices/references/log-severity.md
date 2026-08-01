# Log Severity

Use OpenTelemetry's normalized severity ranges. Preserve the source logger's original level in `SeverityText`, and map its meaning to `SeverityNumber`.

| Severity Number | Range   | Default House Policy                                                                                |
| --------------- | ------- | --------------------------------------------------------------------------------------------------- |
| 1–4             | `TRACE` | Record meaningful per-item and protocol occurrences. Disable these records during normal operation. |
| 5–8             | `DEBUG` | Record named diagnostic occurrences and discrete state changes used during investigation.           |
| 9–12            | `INFO`  | Record significant domain events and lifecycle transitions with bounded context.                    |
| 13–16           | `WARN`  | Record an unusual or degraded condition with meaningful expected impact.                            |
| 17–20           | `ERROR` | Record an error event with high expected impact.                                                    |
| 21–24           | `FATAL` | Record a fatal error such as an application or system crash.                                        |

Use the default house policy only when the active semantic convention does not define a severity. Severity communicates the event's expected impact. It does not communicate the surrounding code's control flow.

Severity applies only after an occurrence warrants a log record. Represent duration-bearing work with a span, and do not create a log merely to report that the span completed.

- Use the severity defined by an operation-specific semantic convention.
- Apply the exception-specific severity model when recording an exception.
- Do not make a record `FATAL` only because an exception was thrown. Reserve `FATAL` for shutdown-level impact.
- A span can end with `Error` status without requiring a `FATAL` log.

Keep the log body stable across occurrences. Bind occurrence-specific dynamic values as attributes so operators can filter and aggregate them. Do not emit a log merely to restate successful span completion or final span attributes.

```jsonc
// BAD: a meaningful occurrence lacks a stable event identity and hides its values in the body.
{
	"body": "Inventory for books fell from 12 to 4 items",
}
```

```jsonc
// GOOD: a meaningful state transition has a stable event identity and occurrence attributes.
{
	"eventName": "inventory.capacity_degraded",
	"body": "Inventory capacity degraded",
	"attributes": {
		"com.acme.inventory.category": "books",
		"com.acme.inventory.previous_count": 12,
		"com.acme.inventory.current_count": 4,
	},
}
```

Do not put full objects, large strings, stack traces, or sensitive values in the body.
