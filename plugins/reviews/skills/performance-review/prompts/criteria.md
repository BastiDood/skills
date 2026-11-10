Find work that the code makes the program do without need.

1. Read the callers that decide how often each code path in scope runs and how large its inputs grow.
2. Flag each anti-pattern below that appears in scope. Name the caller or input that triggers it and state the cost in terms of input size or call frequency.
3. If a claim depends on runtime behavior, propose a measurement. Do not assert the bottleneck.

Skip micro-optimizations off the hot path. A finding without a cost argument is speculation.

### Anti-Patterns

- **Complexity:** nested scans or repeated linear lookups where a map or set suffices.
- **Repeated work:** recomputation in loops or on every render, or the same data parsed, serialized, or copied more than once.
- **I/O:** one query per item, independent calls awaited in sequence, or unbounded reads that should stream or paginate.
- **Memory:** unbounded caches and buffers, whole datasets loaded where a stream suffices, or allocation in hot loops.
- **Blocking:** synchronous I/O or heavy computation on an event loop or UI thread, or locks held across I/O.

### Avoided Work

The most performant work is work the program never does. Before you optimize a slow path, ask whether it needs to exist. Prefer the simpler design with fewer edge cases. Each special case adds a branch, state, and a test path. The branch runs on every call.

Flag work whose result no consumer reads, eager work that callers rarely need, caches or indexes for work that the code could drop, and special cases that the general path already handles.

### Defensive Programming

A defensive check on an already-validated value is a defect. It means no boundary established the invariant. Parse untrusted input once, at the boundary, into a type that encodes the invariant. Downstream code assumes the precondition.

Flag checks on values that their type already guarantees, payloads parsed again between functions, `try`/`catch` blocks or fallback defaults around code with valid inputs, defensive copies of data that nothing mutates, and optional chaining or defaults on required fields. Keep checks at real boundaries: user input, network responses, files, environment variables, and untyped storage.
