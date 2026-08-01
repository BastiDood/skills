# Error Recording

Represent the failed operation consistently across correlated signals. Keep operational failure logs, exception recording, error classification, log severity, and span status as separate decisions.

This policy is language-agnostic. JavaScript examples illustrate syntax only; they are neither normative nor canonical.

OpenTelemetry defines signal fields and operation-outcome semantics. It does not define how an application proves that a causal failure was already recorded. Treat exception ownership, terminal fallback coverage, and duplicate avoidance as house policy built on those standards. Describe their required outcomes without prescribing branding, mutable error state, process-local registries, or another implementation mechanism.

## Separate Operation Outcomes from Exception Records

Set span status and `error.type` independently for every instrumented operation that fails. These fields describe the operation outcome; they do not require an exception payload on every failed span.

Record an exception separately:

- Prefer the lowest useful instrumented operation boundary that owns the failure. The resulting record remains correlated with the most specific span and its local context.
- When that boundary recovers, retries, skips, or otherwise consumes the exception, record it there because no caller will receive it.
- When the exception propagates, preserve it unchanged or through native cause chaining so a caller can retain the complete causal history.
- At the terminal request, job, command, consumer, or process boundary, record an escaping exception when application policy cannot establish that the same causal failure already has an application-owned exception record.

Do not equate an application-specific exception type with proof that telemetry was emitted. Error classification and exception-recording state are separate concerns.

Pass the outermost exception instance available at the owning boundary to the OpenTelemetry Logger API when the language SDK supports it. Otherwise, set the standard `exception.type`, `exception.message`, and `exception.stacktrace` attributes. Do not emit one exception record per cause.

Use the operation-specific event name defined by the active semantic convention. For a custom operation, use a low-cardinality name with an `.exception` suffix. Use `exception` only for a global handler that is not operation-specific.

## Prefer Exception Logs over Span Events

OpenTelemetry is moving exception events from span events to log-based events. For new instrumentation, emit a correlated exception log and set the span outcome separately. Do not also call the trace API's exception-recording method for the same failure.

Existing frameworks and instrumentations can still emit exception span events during the migration. Treat that cross-signal duplication as compatibility behavior; do not reproduce it in application instrumentation.

## Apply One Propagation Rule

When an exception escapes an operation:

1. Mark the span representing that operation as failed.
2. If the exception is unexpected by that boundary, rethrow the original instance unchanged.
3. If the boundary translates an expected failure into caller-relevant meaning, wrap it with the language's native cause mechanism.
4. Emit an exception record only when this operation owns the record under the application's exception-ownership convention.
5. Optionally emit a stack-free contextual log when the boundary adds diagnostically useful metadata that is not preserved by the exception chain.
6. Throw the propagated exception.

Do not catch a mere pass-through only to log or wrap it. Do not use `WARN` merely because a boundary wraps or rethrows an exception. Select severity from expected impact or an operation-specific semantic convention.

## Record Every Recovery

When a boundary swallows an exception and recovers:

1. Emit the exception record with the recovery action and relevant bounded context.
2. Use the exception severity defined by the active semantic convention. Otherwise, select severity from expected impact.
3. Perform the fallback, retry, skip, or other recovery action.

Logging alone is not recovery. A failed attempt span remains failed, while an enclosing operation span remains successful when the recovery fulfills that operation.

## Preserve Retry Failures

Treat each retry attempt as a distinct operation when attempt-level diagnostics matter:

- A failed attempt span ends with `Error` status and its own `error.type`.
- When no lower operation already owns the exception record, record an earlier failed attempt before another attempt replaces its exception. Use the severity defined by the operation-specific convention. `WARN` is appropriate when the exception is expected to be handled by application code.
- Let the final exception propagate to the next owning boundary instead of recording that same attempt again at both the retry layer and the caller.
- Keep the enclosing logical operation successful when a later attempt fulfills it. Mark it failed only when the retry strategy is exhausted and the operation fails.

Do not suppress distinct attempt failures merely because retrying is a recovery strategy. Do not derive severity solely from the attempt number.

## Set Span Status from the Outcome

Set span status to `Error` when the operation represented by the span fails according to its active semantic convention. Leave the status `Unset` when the convention does not classify the outcome as an error.

Set each span's status independently when its operation ends. Do not recursively mutate ancestor spans:

- When one exception escapes several nested operations and causes each to fail, each operation's span ends with `Error`.
- When a parent recovers from a failed child operation, the child span remains `Error` and the parent span remains successful.
- When retries have attempt spans, failed attempt spans end with `Error`; the parent operation span remains successful when a later attempt succeeds.
- Do not mark a span as failed for an internal exception that the represented operation handles successfully.

Do not set span status from log severity.

When an operation fails with an exception, set the span status description only when a bounded, non-sensitive description adds useful information. Do not copy an unfiltered exception message into telemetry. Do not duplicate the status code or `error.type` in the description.

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

Exception attributes are valid at every severity. A `WARN` exception record means that a real exception occurred and application code is expected to handle it; it does not mean the exception lacks a stack trace or causal chain.

## Preserve One Complete Exception Chain

Prefer the language's native exception chaining. Record the outermost exception available at the owning boundary, including every caller-relevant cause already added there.

A complete chain contains every cause reachable when the owning boundary records it. It cannot contain wrappers that a later caller has not created yet. Preserve later translation context through the ancestor span's outcome and, when useful, a stack-free contextual log rather than repeating the earlier exception payload.

OpenTelemetry standardizes `exception.type`, `exception.message`, and `exception.stacktrace`; it does not define a portable record for each cause. Verify that the language SDK, logging bridge, exporter, and backend preserve the complete native chain in one exception record.

When an adapter does not serialize native causes, format the complete chain into the single `exception.stacktrace` value in a shared telemetry adapter. Preserve cause order, bound depth and output size, detect cycles, and keep the outer exception as the primary `exception.type` unless an active semantic convention requires another value.

Do not emit multiple exception logs merely to walk the cause tree. Multiple exception records are justified only when distinct instrumented operations own distinct failures, not because one exception wraps another.

## Illustrative OpenTelemetry JavaScript Mapping

### Initialize the APIs

The following JavaScript syntax illustrates how an OpenTelemetry SDK can express this language-agnostic policy:

```typescript
import { SpanStatusCode } from '@opentelemetry/api';
import { logs, SeverityNumber } from '@opentelemetry/api-logs';
import { ATTR_ERROR_TYPE, ATTR_EXCEPTION_STACKTRACE } from '@opentelemetry/semantic-conventions';

const logger = logs.getLogger('com.acme.orders');
```

Do not treat these imports or signatures as canonical. Use the target language's official OpenTelemetry APIs and verify them against the version used by the application.

The current experimental JavaScript Logs API accepts an `exception` input, and the Logs SDK derives `exception.type`, `exception.message`, and `exception.stacktrace`. It resolves the active context automatically for immediate emission. Pass an explicit captured context when emission is deferred or occurs outside the original execution context. Verify this experimental API against the installed package version.

The JavaScript trace API's `recordException` method creates an exception span event. Do not call it when emitting the same failure as an exception log.

### Record an Exception on the Lowest Useful Span

This operation owns the exception record. The log remains correlated with the active leaf span, while the exception continues to propagate to a caller that is expected to handle it:

This minimal fragment assumes `span` is the active operation span and `error` is an `Error` caught by the surrounding boundary.

```typescript
span.setStatus({
	code: SpanStatusCode.ERROR,
	message: 'Profile load failed',
});
span.setAttribute(ATTR_ERROR_TYPE, error.name);

logger.emit({
	eventName: 'profile.load.exception',
	severityNumber: SeverityNumber.WARN,
	body: 'Profile loading failed',
	exception: error,
	attributes: {
		[ATTR_EXCEPTION_STACKTRACE]: formatCompleteExceptionChain(error),
		'com.acme.operation.phase': 'profile_load',
	},
});

throw error;
```

### Record a Recovered Exception

A recovery boundary supplies the exception because no outer boundary will receive it. The enclosing span remains successful when the fallback fulfills its operation:

This minimal fragment assumes `error` is an `Error` caught by the recovery boundary.

```typescript
logger.emit({
	eventName: 'cache.lookup.exception',
	severityNumber: SeverityNumber.WARN,
	body: 'Cache lookup recovered with defaults',
	exception: error,
	attributes: {
		'com.acme.recovery.action': 'use_defaults',
	},
});

return defaultPreferences;
```

### Propagate an Unexpected Exception Unchanged

When a catch boundary handles only a known failure, preserve every other exception unchanged:

```typescript
try {
	return await loadProfile();
} catch (error) {
	if (!(error instanceof ProfileUnavailableError)) throw error;

	throw new OrderProfileError('Order profile is unavailable', {
		cause: error,
	});
}
```

Do not wrap the fallback branch merely to convert an unexpected exception into an application-specific type.

### Add Stack-Free Translation Context

A translating boundary can attach bounded metadata without repeating the exception payload:

This minimal fragment assumes `span` is the active operation span and `error` is a recognized `Error` caught by the translating boundary.

```typescript
const propagatedError = new OrderCreationError(
	'Order creation failed while loading the customer profile',
	{ cause: error },
);

span.setStatus({
	code: SpanStatusCode.ERROR,
	message: 'Order creation failed',
});
span.setAttribute(ATTR_ERROR_TYPE, propagatedError.name);

logger.emit({
	eventName: 'order.create.failure_translated',
	severityNumber: SeverityNumber.DEBUG,
	body: 'Failure translated for the order domain',
	attributes: {
		'com.acme.error.from_type': error.name,
		'com.acme.error.to_type': propagatedError.name,
	},
});

throw propagatedError;
```

### Keep a Terminal Fallback

The terminal handler always records its own operation outcome. It emits an exception log only when application policy cannot establish that the causal failure was already recorded. `exceptionNeedsRecording` represents that policy decision; it is not an OpenTelemetry API and this guidance intentionally does not prescribe its implementation.

This minimal fragment assumes `rootSpan` represents the terminal operation and `error` is an escaping `Error`.

```typescript
rootSpan.setStatus({
	code: SpanStatusCode.ERROR,
	message: 'Order request failed',
});
rootSpan.setAttribute(ATTR_ERROR_TYPE, error.name);

if (exceptionNeedsRecording(error)) {
	logger.emit({
		eventName: 'order.request.exception',
		severityNumber: SeverityNumber.ERROR,
		body: 'Order request failed',
		exception: error,
		attributes: {
			[ATTR_EXCEPTION_STACKTRACE]: formatCompleteExceptionChain(error),
		},
	});
}
```

The terminal handler still fulfills its control-flow contract by returning an error response, rejecting the job, negatively acknowledging the message, terminating the command, or rethrowing. Telemetry emission does not determine whether the exception is swallowed.

### Preserve the Complete Cause Chain

`formatCompleteExceptionChain` is an application adapter, not an OpenTelemetry API. Use such an adapter only when the configured telemetry integration does not preserve the native cause chain. Supplying `exception.stacktrace` explicitly preserves the complete chain without creating additional records.

The current JavaScript Logs SDK derives only the outer error's code or name, message, and stack. It does not recursively traverse `Error.cause`. Validate the configured logging bridge before adding a formatter because some libraries already include causes in their serialized stack.

```text
failed operation
    -> error.type and Error span status

owned exception record
    -> lowest useful active span
    -> one complete causal chain

propagating ancestor
    -> its own failed outcome
    -> no repeated exception payload

terminal boundary
    -> fallback record when no application-owned record exists
```
