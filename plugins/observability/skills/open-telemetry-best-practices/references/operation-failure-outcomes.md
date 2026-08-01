# Operation Failure Outcomes

Describe every failed operation independently from whether an exception exists or is recorded. Set span status and `error.type` on the span representing that operation; do not use exception emission as a substitute for either field.

This policy is language-agnostic. JavaScript examples illustrate how another language can map the same decisions to its official OpenTelemetry APIs; they are neither normative nor canonical.

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

Do not equate an application-specific exception type with proof that exception telemetry was emitted. Error classification and exception-recording state are separate concerns.

## Illustrative OpenTelemetry JavaScript Mapping

Use semantic-convention constants when the installed package exports them. Verify exact names and stability against the installed version.

```typescript
import { SpanStatusCode } from '@opentelemetry/api';
import { ATTR_ERROR_TYPE } from '@opentelemetry/semantic-conventions';

span.setStatus({
	code: SpanStatusCode.ERROR,
	message: 'Profile load failed',
});
span.setAttribute(ATTR_ERROR_TYPE, error.name);
```

This minimal fragment assumes `span` represents the failed operation and `error` is a recognized `Error`. It records the operation outcome but does not itself emit an exception record.
