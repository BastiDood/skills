# Redundant Tests

Use these diagnostics before adding, retaining, or removing a test or an assertion in a suite that already covers the unit under test.

## Deletion Test

Mentally delete the test or assertion. Keep it only if some realistic defect then passes every remaining test. Judge this per Branch and per Input Class of the unit under test, not per test file or per line of coverage.

- **Branch:** a distinct decision outcome inside the unit under test, such as each arm of a conditional, each state transition, or each early return.
- **Input Class:** a set of inputs that the unit treats identically. Every input in one class reaches the same Branch under the same rule. A boundary value forms its own Input Class. An off-by-one defect changes the outcome at the boundary alone.

Two tests are redundant when they exercise the same Branch with the same Input Class and catch the same defects. Two tests are complementary when they differ in Branch or Input Class. A boundary case and an interior case of one comparison are complementary. One rule applied to a different Input Class, such as empty versus non-empty or negative versus positive, is complementary.

Apply the Deletion Test to each assertion as well. An assertion that repeats what a sibling test already asserts for the same Branch and Input Class adds nothing. An assertion that guards the test's own setup against a vacuous pass is part of that test's oracle, not a duplicate.

## Swapped Arguments

A test that repeats another test's rule with its arguments swapped is complementary only when the keeper names a realistic defect that passes the original and fails the swap. Such a defect exists in two situations:

- The swap reaches a different Branch, such as the other arm of `a > b`.
- The contract fixes how argument order affects the result, and a plausible implementation breaks it, such as subtracting without taking an absolute value for a symmetric distance.

Otherwise delete the swapped test. The burden is on the reviewer who keeps the test. Put the named defect in the test name so the next reviewer can check it.

## Restated Definitions

A test that asserts one constant against another, such as `TOP_INDEX == TABLE.size() - 1`, restates a definition. It exercises no Branch of any unit. When the two constants can drift apart, derive one from the other in the code. Construction then prevents the defect, and the test proves nothing.

## Coincident Fixture Values

A fixture value that equals a constant, a default, or a previously hard-coded value cannot distinguish the code under test from a regression to that value. Such an oracle is not independent. Choose fixture values that differ from each other, from every constant and default in the unit, and from identity values such as `0` and `1` when the rule adds or multiplies them.

```text
// BAD: the parameters replaced hard-coded 8.0 and 0.35. A regression back to those literals still passes.
given a rig constructed with speed 8.0 and turn rate 0.35
when the rig advances one step
then its position matches speed 8.0 and turn rate 0.35

// GOOD: no plausible hard-coded value or swapped parameter produces the expected result.
given a rig constructed with speed 11.0 and turn rate 0.6
when the rig advances one step
then its position matches speed 11.0 and turn rate 0.6
```

## Suite Review

Review a unit's tests as one set:

1. List every Branch and Input Class of the unit under test, including boundaries.
2. Map each test and assertion to the cells it exercises and to the defects it catches.
3. Where several tests occupy one cell and catch the same defects, keep the one with the clearest oracle. Delete the rest.
4. Where a cell is empty and a realistic defect can reach it, admit a test for it.
5. Check each kept test against the coincident-fixture rule.

Prefer adding the missing-Branch test over keeping a duplicate. A duplicate adds maintenance cost and no protection. A test for an unexercised Branch closes a real gap.

## Verdicts

| Verdict | Criteria                                                                                                                                       |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Admit   | Deleting the test or assertion lets a realistic defect pass every remaining test. Its fixture distinguishes the correct code from that defect. |
| Delete  | Every realistic defect it catches already fails a remaining test. A test that exercises no Branch of a project-owned unit also lands here.     |
| Repair  | It targets a defect that no remaining test catches. Its fixture values cannot distinguish the correct code from that defect.                   |

<verdict_examples>

| Scenario                                                                                                   | Verdict | Rationale                                                                                                    |
| ---------------------------------------------------------------------------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------ |
| A test asserts `TOP_GEAR == GEAR_SHARES.size() - 1`.                                                       | Delete  | It restates a definition and exercises no Branch. Derive `TOP_GEAR` from `GEAR_SHARES` if the two can drift. |
| A rig test constructs `Rig.new(8.0, 0.35)` after parameters replaced constants of `8.0` and `0.35`.        | Repair  | The test targets a unique defect. A regression to the old literals still passes. Change the values.          |
| A second test applies the same clamp rule with its arguments swapped, and both reach the same Branch.      | Delete  | No named defect passes the first test and fails the second.                                                  |
| A second test swaps the arguments of an order-dependent comparison, and the swap reaches the other Branch. | Admit   | It exercises a Branch that the first test never reaches.                                                     |
| A third assertion repeats what a sibling test already asserts for the same Branch and Input Class.         | Delete  | Delete the assertion. The sibling test catches every defect that it catches.                                 |
| A test at exactly the limit sits beside a test well inside the limit.                                      | Admit   | An off-by-one comparison passes the interior test and fails the boundary test.                               |
| A proposed test re-claims an item after the claimed item disappears, and no test exercises that Branch.    | Admit   | The Branch is empty. A defect there passes every existing test.                                              |

</verdict_examples>
