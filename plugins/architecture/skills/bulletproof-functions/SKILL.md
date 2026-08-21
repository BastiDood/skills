---
name: bulletproof-functions
description: Exact language-agnostic criteria for setting testable + locally reasonable + maintainable + robust implementation boundaries between functions. Always adhere to these criteria when planning, writing, reviewing, and critiquing abstractions.
metadata:
  youtube:
    channel_name: Logan Smith
    channel_url: https://www.youtube.com/@_noisecode
    video_url: https://www.youtube.com/watch?v=2OMRWPOSw9s
---

# Bulletproof Functions

This reference features a language-agnostic set of highly opinionated guidelines on writing robust bulletproof functions and abstractions that will survive the test of maintainability, testability, and production-grade durability.

## Why do we even write functions to begin with?

This is worth asking because fundamentally, the machine code does not care about them and only operate on assembly instructions. Functions are not hardware-level mechanisms for correctness, performance, or reliability; it has no bearing on the theoretical capabilities of the model of computation.

A shallow view is that functions are meant to deduplicate logic for code reuse, hence style guides dogmatically enforcing the DRY principle (i.e., extraction upon `n > 1` occurrences). This is an unfortunately limited perspective because functions serve to abstract implementation details first and foremost. Functions are "bricks" that are laid out so that callers needn't zoom into the details anymore.

We thus fundamentally agree on the following principles:

- DRY-ness and code reuse are neither sufficient nor necessary conditions for extraction.
- Local reasoning, testability, and abstraction are the primary motivations for extraction, even for an audience of `n = 1` consumers.
- Functions are human-first communication devices for expressing the intent of the code.

## On Function Honesty

To align on a shared glossary for the rest of this reference, we define the following terms:

- **Honest functions** fully communicate their dependents and side effects via their signature (i.e., in-parameters, out-parameters, and return values). These functions especially exhibit testable local reasoning. It is the caller's responsibility to inject all that what would have been external state otherwise.
- **Dishonest functions** touch externalities (e.g., databases, file systems, networks, global RNGs, etc.) that are not entirely communicated via their signature alone. These functions are harder to reason about because it's hard to tell whether their implicit dependents

Dishonest functions remain dishonest despite invoking some honest functions. The converse is false; honest functions that now invoke dishonest functions are themselves dishonest. Therefore, a function call graph must always have honest functions at its leaves.

Structurally, dishonest functions merely orchestrate external systems and inject state handles into honest functions. For maximum testability, it is therefore in our best interests to maximize the number of honest functions while keeping dishonest functions as thin as possible.

### Taxonomy of Honest Functions

#### True Pure Functions

```python
def true_pure_function(x: int) -> int:
    """ Obviously can only compute its output from the input. """
    return x + 1
```

#### Accessor Pure Functions

```python
@dataclass
class Record:
    field: int

def accessor_pure_function(record: Record) -> int:
    """ Accesses and projects the input faithfully. """
    return record.field
```

#### Mutators

```rust
impl<T> Vector<T> {
    /// Clears the vector's contents completely.
    pub fn clear(&mut self) {
        // Impure due to mutations, but totally testable and locally reasonable.
        // The `this` or `self` reference being mutated is faithfully communicated via its signature.
        todo!()
    }
}
```

```cpp
Iterator remove_if(Range&& iter, Predicate&& predicate) {
    // Removes elements from the range that satisfy the predicate, but returns some internally computed state.
    // The returned state ultimately informs the caller how to perform succeeding control flow.
    return some_local_iterator_state;
}
```

### Taxonomy of Dishonest Functions

#### Dishonest Accessor

```cpp
// Function signature implies no inputs, but there is an implicit state dependency on a global resource.
auto get_required_assets() {
    auto assets{...};
    erase_if(assets, &Asset::already_loaded); // dishonest implicit dependency!
    return assets;
}
```

#### Side Effector

```c
// These are functions whose sole purpose is to perform a single unit of side effect/s to an external system.
// Minimize the blast radius of these side effects, and let the more honest orchestrators handle control flow.
void draw_to_screen(const void*);
```

## Framework Hooks

This type of function doesn't fall neatly into an honest/dishonest taxonomy, but it's still a function that orchestrates external systems. The key realization is that most _functions_ typically serve to abstract the implementation details from the call site, whereas _hooks_ (or event handlers) conversely abstract the call site from the implementation details. It's effectively just "glue code" between the application and the framework.

```c
int main() {
    // The C-style `main` function is the canonical example.
    // The underlying library runtime abstracts the entry + exit of the program.
}
```

```tsx
// Next.js App Router's `page.tsx` is a popular example.
export default function Page() {
	return <InternalPage />;
}
```

```ts
// SvelteKit has similar hooks for server-side loaders (`+page.server.ts`), form actions, and server routers (`+server.ts`).

export async function load({ ... }) {
    // ...
}

export const actions = {
    async default({ request }) { ... },
};
```

```c
// Arduino features analogous hook-like abstractions for the embedded runtime.

void setup() {
    // ...
}

void loop() {
    // ...
}
```

## Effective Strategies for Wrangling Functions + Abstractions

Follow the guidelines below religiously. Apply each reference example in the context of the current task.

1. Build your system out of **honest functions**. Inject **dishonesty** at the topmost possible level.
   - [Isolate honest work from dishonest orchestration.](./references/top-level-injection.md)
   - [Hoist dishonesty to the topmost level.](./references/hoisting-external-handles.md)
2. Function signatures should communicate clearly to a human reader (first and foremost)
   - [Substitute long-winded parameter lists with **named parameters** or **transient parameter objects**.](./references/transient-params-struct.md)
3. Show empathy for callers.
   - [Prefer flexible parameter types/interfaces/generics instead of over-constrained concrete types.](./references/flexible-generic-parameters.md)
4. Strongly consider hoisting invariants/preconditions/post-conditions to the type system to make invalid states irrepresentable.
   - [Pass tokens as proof of completion/validity instead of relying on runtime checks.](./references/proof-of-completion-tokens.md)
   - [Use wrapper types to encode validated runtime values in the type system.](./references/validated-wrapper-types.md)
5. Stay at one level of abstraction.
   - [Leverage the "Same Indentation Rule" to ensure that abstraction layers don't _skip_ levels.](./references/same-indentation-rule.md)
