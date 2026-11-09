# Mutation Callback Lifecycle

Put every-invocation policy in the mutation definition and mounted UI consequences at the call site. Trigger one invocation with `mutate` and attach the consequences of that invocation as per-call callbacks.

```tsx
import { useEffect, type ReactNode } from 'react';
import { useMutation } from '@tanstack/react-query';

interface UpdateJobRequest {
	id: string;
	title: string;
}

declare function updateJob(input: UpdateJobRequest): Promise<void>;
declare function closePanel(): void;

interface JobEditorProps {
	onSubmit: (input: UpdateJobRequest) => void;
	disabled: boolean;
	error: Error | null;
}

declare function JobEditor(props: JobEditorProps): ReactNode;

function useUpdateJob() {
	return useMutation({ mutationFn: updateJob });
}

// BAD: aggregate status loses the identity of the invocation that succeeded.
function BadJobForm() {
	const mutation = useUpdateJob();
	useEffect(() => {
		if (mutation.isSuccess) closePanel();
	}, [mutation.isSuccess]);
	return null;
}

// GOOD: the mutation owns status. Its call site owns the mounted consequence.
function JobForm() {
	const { mutate, isPending, error } = useUpdateJob();

	function submit(input: UpdateJobRequest) {
		mutate(input, { onSuccess: closePanel });
	}

	return <JobEditor onSubmit={submit} disabled={isPending} error={error} />;
}
```

Use `onSuccess` for success consequences and `onError` for expected failure presentation. Per-call callbacks do not run if the component unmounts before the mutation finishes. Consecutive `mutate` calls fire them only once, for the last call. Put required persistence, cache policy, and operation-owned cleanup in the mutation definition to run them for every invocation.

Read pending and errors from their existing owner instead of clearing duplicate local flags in `onSettled`. Use `onSettled` only for real operation-owned cleanup required after either result.

## Concurrent Invocations

Use `mutateAsync` only inside a `Promise.*` combinator, when one mutation runs concurrently for several inputs and the caller awaits them together. Only the last call's callbacks fire. Per-call callbacks cannot express that consequence.

```tsx
import { startTransition } from 'react';
import { useMutation } from '@tanstack/react-query';

declare function archiveJob(id: string): Promise<void>;
declare function reportArchiveFailures(count: number): void;
declare function closePanel(): void;

function ArchiveSelectedButton({ jobIds }: { jobIds: readonly string[] }) {
	const { mutate, mutateAsync } = useMutation({ mutationFn: archiveJob });

	// BAD: only the last invocation's callback runs, whichever request settles first.
	function archiveEach() {
		for (const id of jobIds) mutate(id, { onSuccess: closePanel });
	}

	// GOOD: the combinator awaits every invocation before the shared consequence.
	function archiveAll() {
		startTransition(async () => {
			const results = await Promise.allSettled(jobIds.map(async id => await mutateAsync(id)));
			const { length: failureCount } = results.filter(result => result.status === 'rejected');
			if (failureCount > 0) reportArchiveFailures(failureCount);
			else closePanel();
		});
	}

	return (
		<>
			<button type="button" onClick={archiveEach}>
				Archive Each
			</button>
			<button type="button" onClick={archiveAll}>
				Archive Selected
			</button>
		</>
	);
}
```

Run a dependent sequence, where one request needs another's result, inside a single `mutationFn` instead of chaining `mutateAsync` calls. The mutation then has one pending state, one error, and one set of callbacks. Use `mutate` for a single invocation.
