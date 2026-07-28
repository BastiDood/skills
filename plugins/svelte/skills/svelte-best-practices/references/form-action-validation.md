# Form Action Validation

Validate decoded `FormData` in the server action. Return expected validation failures with the safe submitted fields needed to redraw the form.

```typescript
// BAD: throwing parsing turns an expected correction into an error path.
const input = v.parse(CreateItem, submitted);
```

```typescript
// GOOD: return structured validation state to the form.
export const actions = {
	default: async ({ request }) => {
		const submitted = decodeFormData(await request.formData());
		const parsed = v.safeParse(CreateItem, submitted);
		if (parsed.success === false) {
			return fail(400, {
				issues: parsed.issues,
				data: { name: submitted.name, amount: submitted.amount },
			});
		}

		await db.createItem(parsed.output);
	},
} satisfies Actions;
```

Never return credentials, hidden identifiers, or other sensitive fields merely to repopulate the form.
