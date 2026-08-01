---
name: open-telemetry-best-practices
description: Opinionated, language-agnostic OpenTelemetry conventions for span inputs and outputs, named log events, log severity, structured attributes, and operational errors. Use when adding or reviewing OpenTelemetry-compatible telemetry; deciding between span, event, and log attributes; choosing log severity; naming attributes; recording exceptions or cause chains; setting error.type; or setting span status.
---

# OpenTelemetry Best Practices

Treat spans as operation records and named log records as occurrence records. Put safe, bounded, operationally useful inputs, final outputs, and outcomes on the span that represents the operation. Emit a named log event only for a meaningful point-in-time occurrence. Do not infer one signal from another.

Use an established OpenTelemetry semantic convention before defining an application attribute. Keep custom policy visibly separate from OpenTelemetry requirements, and keep telemetry useful across languages, SDKs, collectors, and backends.

Treat this guidance as language-agnostic. Adapt illustrative JavaScript SDK examples to the target language's official OpenTelemetry APIs, native exception chaining, and resource-management conventions.

## Core Conventions

- Preserve trace and span correlation on every log record emitted during an active span.
- Put operation-wide attributes on the span. Include material inputs known at span creation and final output classifications known before the span ends.
- Select attributes by their meaning to the represented operation, not by whether their values came from parameters, local variables, or closures. Exclude implementation-only state.
- Emit a named log event for a meaningful checkpoint, state transition, attempt, exception, or other occurrence that needs its own timestamp, severity, or occurrence-specific attributes.
- Do not emit a successful-completion log merely because a span ended. The ended span, final attributes, and status already record completion.
- Represent a duration-bearing sub-operation with a child span instead of a completion event.
- Keep log bodies stable and concise. Put occurrence-specific queryable context in log attributes, and do not copy every correlated span attribute onto the log.
- Select severity from the significance of the event. Do not select it from whether code catches, retries, falls back, or rethrows.
- Set span status from the outcome of the operation represented by that span.
- Record an exception at the lowest useful operation boundary that owns the failure, where the record retains the most specific span context and bounded local metadata.
- Keep a terminal fallback for an exception that escapes without an application-owned record. Do not assume external libraries recorded a propagated exception.
- Record each causal failure without gaps or repeated exception payloads. Let ancestor operations record their own outcome without copying the same exception stack.
- Preserve unexpected exceptions unchanged. Wrap only when the boundary adds caller-relevant meaning, and preserve the original exception through native cause chaining.
- Prefer exception log records for new instrumentation. Do not also emit the same exception as a span event except during an explicit compatibility migration.
- Preserve every distinct failed retry attempt when it is operationally relevant. Treat recovered attempts and an exhausted operation as separate outcomes without recording one attempt twice.
- Exclude secrets, credentials, personal data, and unbounded payloads from telemetry.

## Library Sources

- Specification GitHub and DeepWiki: `open-telemetry/opentelemetry-specification`
- Semantic Conventions GitHub and DeepWiki: `open-telemetry/semantic-conventions`

## References

Read as many linked references as are relevant to the current task.

- When an event needs a severity and a stable log body, apply the [log severity model](./references/log-severity.md) without coupling severity to control flow.
- When placing or naming queryable context, apply [semantic attribute conventions](./references/structured-attributes.md) to distinguish operation attributes from occurrence attributes before creating application-specific names.
- When an operation fails or an exception is observed, follow [OpenTelemetry error recording](./references/error-recording.md) to keep exception data, `error.type`, and span status consistent without duplicate records.
