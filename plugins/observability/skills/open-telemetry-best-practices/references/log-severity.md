# Log Severity

Use OpenTelemetry's normalized severity ranges. Preserve the source logger's original level in `SeverityText`, and map its meaning to `SeverityNumber`.

This policy is language-agnostic. Select severity from the occurrence's expected impact after deciding that the occurrence warrants a log record.

| Severity Number | Range   | Default House Policy                                                                                |
| --------------- | ------- | --------------------------------------------------------------------------------------------------- |
| 1–4             | `TRACE` | Record meaningful per-item and protocol occurrences. Disable these records during normal operation. |
| 5–8             | `DEBUG` | Record named diagnostic occurrences and discrete state changes used during investigation.           |
| 9–12            | `INFO`  | Record significant domain events and lifecycle transitions with bounded context.                    |
| 13–16           | `WARN`  | Record an unusual or degraded condition with meaningful expected impact.                            |
| 17–20           | `ERROR` | Record an error event with high expected impact.                                                    |
| 21–24           | `FATAL` | Record a fatal error such as an application or system crash.                                        |

Use the default house policy only when the active semantic convention does not define a severity. Severity communicates expected impact; it does not communicate the surrounding code's control flow.

- Use the severity defined by an operation-specific semantic convention.
- Do not select severity from whether code catches, retries, falls back, wraps, or rethrows.
- Do not infer span status from log severity, or severity from span status.
- Do not make a record `FATAL` only because an exception was thrown. Reserve `FATAL` for shutdown-level impact.
- A span can end with `Error` status without requiring a `FATAL` log.

For exception events when no operation-specific convention applies:

- Use `FATAL` when the exception usually causes application shutdown.
- Use `ERROR` when application code does not handle the exception and it does not cause shutdown.
- Use `WARN` when application code is expected to handle the exception.
- Use `DEBUG` when the exception does not indicate an actual issue, such as expected cancellation.
- Use `INFO` or `TRACE` only when an application-specific exception event's expected impact fits that severity.

Always provide `SeverityNumber` for an exception event. Exception attributes are valid at every severity. A `WARN` exception record means that a real exception occurred and application code is expected to handle it; it does not mean the exception lacks a stack trace or causal chain.

Severity applies only after an occurrence warrants a log record. Represent duration-bearing work with a span, and do not create a log merely to report that the span completed.
