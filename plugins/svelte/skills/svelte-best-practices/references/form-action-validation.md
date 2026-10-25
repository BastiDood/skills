# Form Action Validation

Validate decoded `FormData` in the server action, even when its browser caller is typed or performs client validation. Authorization also belongs to the server boundary. Return expected validation failures with the safe submitted fields needed to redraw the form.

```typescript
import { fail, type Actions } from '@sveltejs/kit';
import { decode } from 'decode-formdata';
import * as v from 'valibot';

const CreateItem = v.object({ name: v.pipe(v.string(), v.minLength(1)), amount: v.number() });
type CreateItem = v.InferOutput<typeof CreateItem>;

interface SubmittedFields {
	name?: string;
	amount?: string;
}

declare function createItem(input: CreateItem): Promise<void>;

// BAD: throwing parsing turns an expected correction into an error path.
function parseUnchecked(formData: FormData) {
	return v.parse(CreateItem, decode(formData, { numbers: ['amount'] }));
}

// GOOD: return structured issues and only safe text fields to the form.
export const actions = {
	default: async ({ request }) => {
		const formData = await request.formData();
		const submitted = decode(formData, { numbers: ['amount'] });
		const parsed = v.safeParse(CreateItem, submitted);
		if (parsed.success === false) {
			const data: SubmittedFields = {};
			for (const field of ['name', 'amount'] as const) {
				const value = formData.get(field);
				if (typeof value === 'string') data[field] = value;
			}
			return fail(400, { issues: parsed.issues, data });
		}

		await createItem(parsed.output);
	},
} satisfies Actions;
```

Pass `parsed.output` to internal work without reparsing it. Never return credentials, hidden identifiers, or other sensitive fields merely to repopulate the form.
