# Mutation Form Integration

Intercept `onSubmit` for both controlled and uncontrolled forms. Prevent default navigation, decode and parse a fresh `FormData` snapshot, then pass the validated domain input through mutation variables. Controlled state belongs to live rendered feedback; submission must not assemble its payload from that changing state.

```tsx
import { useMutation } from '@tanstack/react-query';
import { decode } from 'decode-formdata';
import type { FormEvent } from 'react';
import * as v from 'valibot';

const UpdateJobInput = v.object({ title: v.pipe(v.string(), v.minLength(1)) });
type UpdateJobInput = v.InferOutput<typeof UpdateJobInput>;

declare function updateJob(input: UpdateJobInput): Promise<void>;

function useUpdateJob() {
	return useMutation({ mutationFn: updateJob });
}

function JobForm() {
	const { mutate, isPending, error } = useUpdateJob();

	function submit(event: FormEvent<HTMLFormElement>) {
		event.preventDefault();
		const formData = new FormData(event.currentTarget);
		const input = v.parse(UpdateJobInput, decode(formData));
		mutate(input);
	}

	return (
		<form onSubmit={submit}>
			<label>
				Title
				<input name="title" required />
			</label>
			<button type="submit" disabled={isPending}>
				Save
			</button>
			<p role="status">{isPending ? 'Saving...' : null}</p>
			{error !== null ? <p role="alert">{error.message}</p> : null}
		</form>
	);
}
```

Keep configuration stable and changing input in mutation variables. Use per-call callbacks for mounted UI consequences. Read pending and error state from the mutation instead of recreating it locally. A single form submission is one invocation and uses `mutate`, not `mutateAsync`. Reset drafts only after the intended successful outcome.
