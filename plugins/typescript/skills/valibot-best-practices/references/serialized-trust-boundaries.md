# Serialized Trust Boundaries

Validate serialized data when it enters the application: network responses, request bodies, `FormData`, JSON, JSONB, webhooks, environment-provided strings, and persisted opaque payloads.

Authenticated network payloads remain external input. Authentication identifies the sender; it does not prove payload shape.

Parse once at the controlled trust boundary, then retain the validated contract through internal calls. Do not re-parse values already constructed or validated and retained within the typed domain. A typed caller does not make an externally callable endpoint trusted: direct hostile requests must still encounter its input validation and authorization.

When a framework already validates the same input at that boundary, use its validated value rather than adding a second parser inside the handler. Verify that the framework actually performs runtime validation; a type annotation alone supplies no evidence.

```typescript
import * as v from 'valibot';

const User = v.object({ id: v.pipe(v.string(), v.uuid()), name: v.string() });

// A provider response remains external even after authentication.
async function fetchProviderUser(url: string) {
	const response = await fetch(url);
	if (response.ok === false) throw new Error(`Provider request failed: ${response.status}`);
	const responseBody: unknown = await response.json();
	return v.parse(User, responseBody);
}
```

Treat a serialization boundary as the place where untrusted values become domain values.
