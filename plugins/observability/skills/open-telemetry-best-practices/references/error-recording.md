# Error Recording

Represent the failed operation consistently across correlated signals. Keep exception recording, error classification, log severity, and span status as separate decisions.

## Record the Exception Once

When an active semantic convention requires an exception event, emit it as a log record through the OpenTelemetry Logger API and associate it with the corresponding span context. Pass the exception instance to the SDK when the language API supports it. Otherwise, use the standard `exception.type`, `exception.message`, and `exception.stacktrace` attributes.

Do not record the same exception in several layers. Do not duplicate the full exception as both a log record and a span event. Do not record an exception handled internally by instrumentation when the represented operation succeeds.

Use the operation-specific event name defined by the active semantic convention. For a custom operation, use a low-cardinality name with an `.exception` suffix. Use `exception` for a global handler that is not specific to an operation.

## Classify the Failed Operation

Set `error.type` when the represented operation fails. Use a predictable, low-cardinality value:

- Use the exception type when an exception identifies the failure.
- Use the protocol or domain error identifier defined by the active semantic convention.
- Do not use an exception message or another unbounded value.
- Use the same `error.type` on correlated spans and metrics for the same failed operation.
- Omit `error.type` when the represented operation succeeds.

## Set Span Status from the Outcome

Set span status to `Error` when the operation represented by the span fails according to its active semantic convention. Leave the status `Unset` when the convention does not classify the outcome as an error.

Do not set span status from log severity. Do not mark a span as failed only because an internal attempt failed when the represented operation recovered successfully. When retries have their own attempt spans, each attempt span can report its own outcome while the parent operation span reports the final outcome.

When an operation fails with an exception, set the span status description to the exception message. Treat that message as potentially sensitive and apply the configured telemetry filtering policy. For other failures, set a predictable description only when it adds non-sensitive information. Do not duplicate the status code or `error.type` in the description.

## Keep Severity Independent

Use the severity defined by the operation-specific semantic convention. Otherwise, select exception severity from its expected impact:

- Use `FATAL` for an exception that usually causes application shutdown.
- Use `ERROR` for an exception that application code does not handle and that does not cause application shutdown.
- Use `WARN` for an exception that application code is expected to handle.
- Use `DEBUG` for an exception that does not indicate an actual issue, such as expected cancellation.
- Use `INFO` or `TRACE` only for an application-specific exception event whose expected impact fits that severity.

Always provide `SeverityNumber` for an exception event. Do not infer span status from this severity.

```text
operation outcome
    -> error.type on failed spans and metrics
    -> span status for the represented operation

exception event
    -> one correlated log record with exception details
    -> severity for that log event
```
