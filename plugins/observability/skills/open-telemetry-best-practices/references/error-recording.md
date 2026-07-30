# Error Recording

Represent the failed operation consistently across correlated signals. Keep operational failure logs, exception recording, error classification, log severity, and span status as separate decisions.

This policy is language-agnostic. JavaScript examples illustrate syntax only; they are neither normative nor canonical.

OpenTelemetry defines the signal fields and operation-outcome semantics. It does not require the ownership topology below. Treat early stack-free failure logs, root-span exception ownership, and recovery recording as house policy built on those standards.

## Separate Operational Failure Logs from Exception Records

Emit a structured operational failure log at the earliest boundary that can classify the failed operation and attach useful local context. Correlate it with that boundary's active span. Omit the exception payload, `exception.stacktrace`, and equivalent stack-bearing fields. Do not repeat the same operational failure at forwarding layers.

Record the exception separately and once:

- For a propagated failure, the root span of the owned operation emits one exception record after the native exception chain is complete. An owned operation is an independently handled request, job, command, consumer invocation, or equivalent boundary. Its root span can be a child span in a distributed trace.
- For a recovered failure, the recovery boundary emits the exception record because the exception does not reach the owned operation's root span.

Associate the exception record with the span that owns that terminal decision. Pass the outermost exception instance to the OpenTelemetry Logger API when the language SDK supports it. Otherwise, set the standard `exception.type`, `exception.message`, and `exception.stacktrace` attributes.

Do not emit one exception record per cause. Do not duplicate the exception as both a log record and a span event. Use the operation-specific event name defined by the active semantic convention. For a custom operation, use a low-cardinality name with an `.exception` suffix. Use `exception` only for a global handler that is not operation-specific.

## Apply One Propagation Rule

When an exception escapes an operation:

1. Mark the span representing that operation as failed.
2. Preserve the exception unchanged or wrap it with the language's native cause mechanism when caller-relevant context is added.
3. Optionally emit a stack-free `DEBUG` log when the boundary adds diagnostically useful context, such as a domain translation. Do not log a mere pass-through.
4. Throw the propagated exception.

Do not use `WARN` merely because a boundary wraps or rethrows an exception. Select severity from observable impact.

## Record Every Recovery

When a boundary swallows an exception and recovers:

1. Emit its terminal exception record with the recovery action and relevant bounded context.
2. Use `DEBUG` when the recovery has no meaningful observable impact.
3. Escalate to `WARN` or `ERROR` only when the impact warrants that severity.
4. Perform the fallback, retry, skip, or other recovery action.

Logging alone is not recovery. A failed attempt span remains failed, while an enclosing operation span remains successful when the recovery fulfills that operation.

## Set Span Status from the Outcome

Set span status to `Error` when the operation represented by the span fails according to its active semantic convention. Leave the status `Unset` when the convention does not classify the outcome as an error.

Set each span's status independently when its operation ends. Do not recursively mutate ancestor spans:

- When one exception escapes several nested operations and causes each to fail, each operation's span ends with `Error`.
- When a parent recovers from a failed child operation, the child span remains `Error` and the parent span remains successful.
- When retries have attempt spans, failed attempt spans end with `Error`; the parent operation span remains successful when a later attempt succeeds.
- Do not mark a span as failed for an internal exception that the represented operation handles successfully.

Do not set span status from log severity.

When an operation fails with an exception, set the span status description to the exception message. Treat that message as potentially sensitive and apply the configured telemetry filtering policy. For other failures, set a predictable description only when it adds non-sensitive information. Do not duplicate the status code or `error.type` in the description.

## Classify the Failed Operation

Set `error.type` when the represented operation fails. Use a predictable, low-cardinality value:

- Use the exception type when an exception identifies the failure.
- Use the protocol or domain error identifier defined by the active semantic convention.
- Do not use an exception message or another unbounded value.
- Use the same `error.type` on correlated spans and metrics for the same failed operation.
- Omit `error.type` when the represented operation succeeds.

## Keep Severity Independent

Use the severity defined by the operation-specific semantic convention. Otherwise, select exception severity from its expected impact:

- Use `FATAL` for an exception that usually causes application shutdown.
- Use `ERROR` for an exception that application code does not handle and that does not cause application shutdown.
- Use `WARN` for an exception that application code is expected to handle.
- Use `DEBUG` for an exception that does not indicate an actual issue, such as expected cancellation.
- Use `INFO` or `TRACE` only for an application-specific exception event whose expected impact fits that severity.

Always provide `SeverityNumber` for an exception event. Do not infer span status from this severity.

## Preserve One Complete Exception Chain

Prefer the language's native exception chaining. Pass the outermost exception to the owning boundary after all caller-relevant wrappers have been added.

OpenTelemetry standardizes `exception.type`, `exception.message`, and `exception.stacktrace`; it does not define a portable record for each cause. Verify that the language SDK, logging bridge, exporter, and backend preserve the complete native chain in one exception record.

When an adapter does not serialize native causes, format the complete chain into the single `exception.stacktrace` value in a shared telemetry adapter. Preserve cause order, bound depth and output size, detect cycles, and keep the outer exception as the primary `exception.type` unless an active semantic convention requires another value.

## Illustrative OpenTelemetry JavaScript Mapping

### Initialize the APIs

The following JavaScript syntax illustrates how an OpenTelemetry SDK can express this language-agnostic policy:

```typescript
import { context, SpanStatusCode, trace } from '@opentelemetry/api';
import { logs, SeverityNumber } from '@opentelemetry/api-logs';

const tracer = trace.getTracer('com.acme.orders');
const logger = logs.getLogger('com.acme.orders');
```

Do not treat these imports or signatures as canonical. Use the target language's official OpenTelemetry APIs and verify them against the version used by the application.

### Record an Early Operational Failure

An early operational failure log carries local context but no exception payload:

```typescript
span.setStatus({
	code: SpanStatusCode.ERROR,
	message: error.message,
});
span.setAttribute('error.type', error.name);

logger.emit({
	eventName: 'profile.load.failed',
	context: context.active(),
	severityNumber: SeverityNumber.ERROR,
	severityText: 'ERROR',
	body: 'Profile loading failed',
	attributes: {
		'error.type': error.name,
		'com.acme.customer.id': customerId,
	},
});

throw error;
```

### Record a Recovered Exception

A recovery boundary supplies the exception because no outer boundary will receive it. The enclosing span remains successful when the fallback fulfills its operation:

```typescript
logger.emit({
	eventName: 'cache.lookup.exception',
	context: context.active(),
	severityNumber: SeverityNumber.DEBUG,
	severityText: 'DEBUG',
	body: 'Cache lookup recovered with defaults',
	exception: error,
	attributes: {
		'com.acme.recovery.action': 'use_defaults',
		'com.acme.customer.id': customerId,
	},
});

return defaultPreferences;
```

### Propagate an Exception

A propagation boundary can enrich the exception and optionally emit stack-free debug metadata:

```typescript
const propagatedError = new OrderCreationError(
	'Order creation failed while loading the customer profile',
	{ cause: error },
);

span.setStatus({
	code: SpanStatusCode.ERROR,
	message: propagatedError.message,
});
span.setAttribute('error.type', propagatedError.name);

logger.emit({
	eventName: 'order.create.failure_translated',
	context: context.active(),
	severityNumber: SeverityNumber.DEBUG,
	severityText: 'DEBUG',
	body: 'Failure translated for the order domain',
	attributes: {
		'com.acme.error.from_type': error.name,
		'com.acme.error.to_type': propagatedError.name,
	},
});

throw propagatedError;
```

### Record the Exception at the Root Span

The owned operation's root span emits the one complete exception record while that span remains active:

```typescript
rootSpan.setStatus({
	code: SpanStatusCode.ERROR,
	message: error.message,
});
rootSpan.setAttribute('error.type', error.name);

logger.emit({
	eventName: 'order.request.exception',
	context: context.active(),
	severityNumber: SeverityNumber.ERROR,
	severityText: 'ERROR',
	body: 'Order request failed',
	exception: error,
	attributes: {
		'exception.stacktrace': formatCompleteExceptionChain(error),
		'com.acme.customer.id': customerId,
	},
});
```

### Preserve the Complete Cause Chain

`formatCompleteExceptionChain` is an application adapter, not an OpenTelemetry API. Use such an adapter only when the configured telemetry integration does not preserve the native cause chain. Supplying `exception.stacktrace` explicitly preserves the complete chain without creating additional records.

Omit the explicit `context` only when the configured SDK is known to attach `context.active()` automatically. Do not also call `Span#recordException()` for the same failure.

## Sources

- [OpenTelemetry exception log conventions](https://opentelemetry.io/docs/specs/semconv/exceptions/exceptions-logs/)
- [OpenTelemetry general error recording](https://opentelemetry.io/docs/specs/semconv/general/recording-errors/)

```text
operation outcome
    -> error.type on failed spans and metrics
    -> span status for the represented operation

early operational failure
    -> one stack-free log on the span with useful local context

terminal exception decision
    -> one correlated exception log with the complete chain
    -> severity for that log event
```
