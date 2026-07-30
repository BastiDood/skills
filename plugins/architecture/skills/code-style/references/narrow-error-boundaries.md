# Narrow Error Boundaries

Catch only an expected failure from the smallest operation that can produce it. Preserve unexpected failures and their original causes. Ensure that every operational error or exception reaches a configured observability sink exactly once. Exclude cancellation, interruption, and other platform-defined control-flow signals from this failure policy.

```typescript
// BAD: a broad catch hides request-building and persistence defects.
async function loadBroadly() {
	try {
		const request = buildRequest(input);
		const response = await send(request);
		await save(response);
	} catch {
		return emptyResult;
	}
}

// GOOD: only the expected network failure is translated.
async function load() {
	const request = buildRequest(input);
	let response: Response;
	try {
		response = await send(request);
	} catch (error) {
		if (error instanceof NetworkUnavailable) {
			logger.exception(error).warn('Request can be retried', { requestId });
			return retryableFailure(error);
		}
		throw error;
	}
	await save(response);
}

// GOOD: the designated outer boundary records an unexpected failure once.
async function runLoadJob() {
	try {
		await load();
	} catch (error) {
		logger.exception(error).error('Load job failed', { requestId });
		throw error;
	}
}
```

Log at the boundary that has enough context to classify the failure and decide whether to recover, translate, or terminate. That boundary owns the telemetry record:

- For a recoverable failure: record the original error and relevant operational context, then perform the explicit fallback, retry, skip, or other recovery action. Logging alone is not sufficient recovery.
- For a non-recoverable failure: record the original error and relevant operational context, then rethrow it unchanged or wrap it with the original error as its cause to preserve the exception chain + stack trace.
- For an error that propagates without a local decision: do not catch or log it in mere forwarding layers. If no inner boundary owns its telemetry record, the designated outer error boundary must record it before termination.

The first boundary that decides recovery, translation, or termination owns the telemetry record. After a boundary records a failure and rethrows it, every outer forwarding boundary must preserve the failure without recording it again.

Use a structured logger, trace or span event, error tracker, or another configured sink that makes the failure available to operators. Plain console output qualifies only when the application routes it into its observability pipeline.

```typescript
// BAD: the fallback swallows the failure without an observable record.
try {
	result = await loadProfile(userId);
} catch {
	result = anonymousProfile;
}

// GOOD: the boundary records and handles the recoverable failure.
try {
	result = await loadProfile(userId);
} catch (error) {
	logger.exception(error).error('Profile load failed; using anonymous profile', { userId });
	result = anonymousProfile;
}

// GOOD: the decision-owning boundary records a non-recoverable failure and preserves its chain.
try {
	await saveProfile(profile);
} catch (error) {
	logger.exception(error).error('Profile persistence failed', { userId: profile.userId });
	throw new ProfilePersistenceError('Could not persist profile', { cause: error });
}
```

Do not add `try` blocks preemptively. Do not catch an error only to repeat telemetry already emitted by its owning boundary. Let pass-through layers propagate failures without log noise.
