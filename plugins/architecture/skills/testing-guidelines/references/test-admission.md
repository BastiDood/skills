# Test Admission

Test behavior that the project owns. Assume third-party libraries are correct and already tested.

Add a test only when it can fail because project-owned behavior is wrong.

Project-owned behavior includes:

- Business rules and calculations.
- State-machine transitions and exhaustive decisions.
- Transformations that add domain meaning.
- Selection, retry, fallback, caching, and persistence policy.
- Error translation that adds a stable project contract.

Do not add a test when the implementation only forwards values to a dependency and returns its result unchanged.
