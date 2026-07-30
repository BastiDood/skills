# Log Severity

Use OpenTelemetry's normalized severity ranges. Preserve the source logger's original level in `SeverityText`, and map its meaning to `SeverityNumber`.

| Severity Number | Range   | Default House Policy                                                                                                          |
| --------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------- |
| 1–4             | `TRACE` | Record per-item diagnostics, protocol internals, and granular computed values. Disable these records during normal operation. |
| 5–8             | `DEBUG` | Record diagnostic checkpoints and external interaction summaries used during investigation.                                   |
| 9–12            | `INFO`  | Record significant operation and lifecycle milestones with bounded summaries.                                                 |
| 13–16           | `WARN`  | Record an unusual or degraded condition with meaningful expected impact.                                                      |
| 17–20           | `ERROR` | Record an error event with high expected impact.                                                                              |
| 21–24           | `FATAL` | Record a fatal error such as an application or system crash.                                                                  |

Use the default house policy only when the active semantic convention does not define a severity. Severity communicates the event's expected impact. It does not communicate the surrounding code's control flow.

- Use the severity defined by an operation-specific semantic convention.
- Apply the exception-specific severity model when recording an exception.
- Do not make a record `FATAL` only because an exception was thrown. Reserve `FATAL` for shutdown-level impact.
- A span can end with `Error` status without requiring a `FATAL` log.

Keep the log body stable across occurrences. Bind dynamic values as attributes so operators can filter and aggregate them.

```jsonc
// BAD: dynamic context exists only in the body.
{
	"body": "Catalog extraction for ABC completed with 12 items",
}
```

```jsonc
// GOOD: the body identifies the event and attributes carry queryable context.
{
	"body": "Catalog extraction completed",
	"attributes": {
		"com.acme.catalog.symbol": "ABC",
		"com.acme.catalog.item_count": 12,
	},
}
```

Do not put full objects, large strings, stack traces, or sensitive values in the body.
