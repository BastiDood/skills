# Structured Attributes

Use the OpenTelemetry attribute registry and the active semantic convention for the instrumented operation before defining a custom attribute. Reuse the exact standard name, type, meaning, and value constraints.

Use official operation namespaces only for their defined semantics. For example:

- Use `http.request.method` for the normalized HTTP method.
- Use `http.response.status_code` for the HTTP response status code.
- Use `error.type` for the predictable, low-cardinality class of a failed operation.
- Use resource attributes such as `service.name` for the entity that produced telemetry.

Do not treat `request.*`, `response.*`, `entity.*`, or `result.*` as generic application namespaces. A name that resembles a semantic convention is not a semantic convention.

When no standard attribute applies:

- Prefix organization-wide attributes with the organization's reverse domain name.
- Prefix internal application attributes with a unique application name when an organization-wide namespace is not available.
- Use lowercase dot-delimited namespaces.
- Use snake_case inside a multi-word namespace component.
- Keep names precise and stable.
- Keep values bounded and suitable for aggregation.
- Avoid an existing OpenTelemetry namespace because a future convention can collide with the custom name.

```jsonc
// BAD: the names are ambiguous and can collide with semantic conventions.
{
	"response_status": 200,
	"entity_count": 12,
}
```

```jsonc
// GOOD: the HTTP value uses a standard name and the domain value uses an application namespace.
{
	"http.response.status_code": 200,
	"com.acme.catalog.item_count": 12,
}
```

Do not encode secrets, credentials, personal data, complete request or response bodies, or other unbounded data as attributes.
