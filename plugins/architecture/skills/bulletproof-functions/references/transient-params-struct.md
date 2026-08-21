# Motivating Example

Consider a hypothetically opaque `set_timer` function.

```cpp
void set_timer(void*, const string&, float, bool, bool, bool); // what?

int main() {
    set_timer(
        nullptr,
        "id",
        5, // implicit coercion?
        true,
        false, // prone to switch arguments of the same type
        true, // which argument was this again?
    );
}
```

The example is comically exaggerated, but it does happen in practice!

## Solution: Use a Transient Parameter Object

A cleaner formulation that communicates clearly to a human reader is a **transient parameter object**. The only caveat is to be mindful of ownership semantics when copying/moving values into the transient parameter object.

```cpp
struct SetTimerParams {
    void* context;
    std::string id;
    float interval;
    bool should_delay;
    bool should_loop;
    bool can_retrigger;
};

void set_timer(SetTimerParams params);

int main() {
    set_timer(SetTimerParams{
        .context = nullptr,
        .id = "id",
        .interval = 5,
        .should_delay = true,
        .should_loop = false,
        .can_retrigger = true,
    });
}
```

Note that transient parameter objects are analogous to named parameters in languages like Python (via `TypedDict` for `**kwargs`).

```python
from typing import Any

class SetTimerParams(TypedDict):
    context: Any
    id: str
    interval: float
    should_delay: bool
    should_loop: bool
    can_retrigger: bool

set_timer(
    context=None,
    id="id",
    interval=5,
    should_delay=True,
    should_loop=False,
    can_retrigger=True,
)
```

## Important: Not to be Confused for Passing God Classes

This technique should not be confused for the anti-pattern of passing entire class instances as parameters. Not only does this oversubscribe the function's interface (i.e., many fields/methods are irrelevant), but it also implies coupling between the function and the class, which is typically not the intention.

```cpp
void set_timer(const EntireClass& object);
```

The operative keyword here is **transient**. Any caller of any shape should be able to pass named parameters to the function; the parameter object only cares that the values are mapped correctly.
