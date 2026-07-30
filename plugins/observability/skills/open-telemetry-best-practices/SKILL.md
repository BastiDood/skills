---
name: open-telemetry-best-practices
description: Opinionated, language-agnostic OpenTelemetry conventions for log severity, structured attributes, and recording operational errors across logs and spans. Use when adding or reviewing OpenTelemetry-compatible telemetry, choosing log severity, naming attributes, recording exceptions or cause chains, setting error.type, or setting span status.
---

# OpenTelemetry Best Practices

Treat logs, spans, and metrics as correlated signals with separate semantics. Represent events as structured log records, describe the operation outcome with span status, and describe queryable context with semantic attributes. Do not infer one signal from another.

Use an established OpenTelemetry semantic convention before defining an application attribute. Keep custom policy visibly separate from OpenTelemetry requirements, and keep telemetry useful across languages, SDKs, collectors, and backends.

Treat this guidance as language-agnostic. Adapt illustrative JavaScript SDK examples to the target language's official OpenTelemetry APIs, native exception chaining, and resource-management conventions.

## Core Conventions

- Preserve trace and span correlation on every log record emitted during an active span.
- Keep log bodies stable and concise. Put identifiers, counts, states, durations, and error classifications in attributes.
- Select severity from the significance of the event. Do not select it from whether code catches, retries, falls back, or rethrows.
- Set span status from the outcome of the operation represented by that span.
- Emit useful operational failure logs as early as possible without repeating exception payloads.
- Record one complete exception chain at the boundary that owns the failed operation. Do not copy the same stack trace into several signals or layers.
- Exclude secrets, credentials, personal data, and unbounded payloads from telemetry.

## Library Sources

- Specification GitHub and DeepWiki: `open-telemetry/opentelemetry-specification`
- Semantic Conventions GitHub and DeepWiki: `open-telemetry/semantic-conventions`

## References

Read as many linked references as are relevant to the current task.

- When an event needs a severity and a stable log body, apply the [log severity model](./references/log-severity.md) without coupling severity to control flow.
- When telemetry needs queryable context, apply [semantic attribute conventions](./references/structured-attributes.md) before creating application-specific names.
- When an operation fails or an exception is observed, follow [OpenTelemetry error recording](./references/error-recording.md) to keep exception data, `error.type`, and span status consistent without duplicate records.
