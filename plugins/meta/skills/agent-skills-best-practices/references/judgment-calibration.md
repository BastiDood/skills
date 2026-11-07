# Judgment Calibration

Calibrate every classification scheme (e.g., severity, confidence, or priority scale) with few-shot examples. A list of labels alone lets each run choose its own thresholds. Give a couple of few-shot examples that use the scheme's categories as the axes of consideration: each example shows an input, the category it receives, and why it lands there rather than in a neighboring category.

<calibration_examples>

```markdown
<!-- BAD: labels without calibration. -->

Assign each finding a severity: `critical`, `high`, `medium`, or `low`.
```

```markdown
<!-- GOOD: few-shot examples span the categories and explain each boundary. -->

Assign each finding a severity: critical, high, medium, or low.

<severity_examples>

- **A migration drops a column that production still reads.** Severity: `critical`, because every request on the main path fails after the deploy.
- **A retry loop never stops while the upstream service is down.** Severity: `high`, because it exhausts workers, but only during an outage.
- **An error message names the wrong field for one malformed input.** Severity: `medium`, because the user can still recover, and only on a rare path.
- **A log line omits the request ID.** Severity: `low`, because no user-facing behavior changes.

</severity_examples>
```

</calibration_examples>
