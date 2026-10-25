# Client Form Decoding

For an SPA form, take a fresh `FormData` snapshot of the named controls in the submit driver, independently of any bound input state. Decode and validate it before invoking the mutation. Keep mutations as minimal wrappers over the asynchronous boundary, receiving validated domain input rather than parsing arguments. With `decode-formdata`, declare numeric, date, and array fields so validation receives the intended runtime representations.

Within the SPA component's setup, the schema defines the domain input and the mutation consumes only validated values:

```typescript
import { createMutation } from '@tanstack/svelte-query';
import { decode } from 'decode-formdata';
import * as v from 'valibot';

const CreateItem = v.object({
	name: v.pipe(v.string(), v.minLength(1)),
	amount: v.number(),
	quantity: v.number(),
	datedAt: v.date(),
	tags: v.array(v.string()),
});
type CreateItem = v.InferOutput<typeof CreateItem>;

declare function createItem(input: CreateItem): Promise<void>;
declare function showValidationIssues(issues: readonly v.InferIssue<typeof CreateItem>[]): void;

const mutation = createMutation(() => ({ mutationFn: createItem }));

// BAD: a domain-only mutation receives decoded but unvalidated transport data.
function submitUnchecked(form: HTMLFormElement) {
	const submitted = decode(new FormData(form));
	mutation.mutate(submitted);
}

// GOOD: the submit driver decodes and validates once before domain work.
function submit(form: HTMLFormElement) {
	const submitted = decode(new FormData(form), {
		numbers: ['amount', 'quantity'],
		dates: ['datedAt'],
		arrays: ['tags'],
	});
	const parsed = v.safeParse(CreateItem, submitted);
	if (parsed.success) {
		mutation.mutate(parsed.output);
	} else {
		showValidationIssues(parsed.issues);
	}
}
```

Client validation improves feedback but cannot establish server trust: every public endpoint still validates and authorizes its incoming request. Internal operations consume their boundary's validated result without reparsing.
