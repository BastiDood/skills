# Judgment Calibration

Define every classification scheme (e.g., severity, confidence, or priority scale) as a table of its labels and their criteria, then calibrate it with a table of few-shot examples. A list of labels alone lets each run choose its own thresholds. Each example row gives a "Scenario", one column per label that it assigns, and a "Rationale" that says why the row lands there rather than in a neighboring label.

<calibration_examples>

```markdown
<!-- BAD: labels without criteria or calibration. -->

Assign each finding a severity: `critical`, `high`, `medium`, or `low`.
```

```markdown
<!-- GOOD: the scheme is a table with examples that span its labels. -->

| Severity | Criteria                                                  |
| -------- | --------------------------------------------------------- |
| critical | The main path fails for every user.                       |
| high     | A failure that needs a rare condition, such as an outage. |
| medium   | A recoverable failure on a rare path.                     |
| low      | No user-facing behavior changes.                          |

<severity_examples>

| Scenario                                                        | Severity | Rationale                                              |
| --------------------------------------------------------------- | -------- | ------------------------------------------------------ |
| A migration drops a column that production still reads.         | critical | Every request on the main path fails after the deploy. |
| A retry loop never stops while the upstream service is down.    | high     | It exhausts workers, but only during an outage.        |
| An error message names the wrong field for one malformed input. | medium   | The user can still recover, and only on a rare path.   |
| A log line omits the request ID.                                | low      | No user-facing behavior changes.                       |

</severity_examples>
```

</calibration_examples>
