# Dependency Cleanup Report

{{SCOPE_SUMMARY}}

## Remove

| Package       | Finding     | Risk     | Impact     | Verdict | Rationale     |
| ------------- | ----------- | -------- | ---------- | ------- | ------------- |
| `{{PACKAGE}}` | {{FINDING}} | {{RISK}} | {{IMPACT}} | Remove  | {{RATIONALE}} |

## Replace

| Package                         | Finding     | Risk     | Impact     | Verdict | Rationale     |
| ------------------------------- | ----------- | -------- | ---------- | ------- | ------------- |
| `{{PACKAGE}}` → `{{SUCCESSOR}}` | {{FINDING}} | {{RISK}} | {{IMPACT}} | Replace | {{RATIONALE}} |

## Move

| Package       | Finding                           | Risk     | Impact     | Verdict | Rationale     |
| ------------- | --------------------------------- | -------- | ---------- | ------- | ------------- |
| `{{PACKAGE}}` | {{FROM_SECTION}} → {{TO_SECTION}} | {{RISK}} | {{IMPACT}} | Move    | {{RATIONALE}} |

## Dedupe

| Package       | Finding                   | Risk     | Impact     | Verdict | Rationale     |
| ------------- | ------------------------- | -------- | ---------- | ------- | ------------- |
| `{{PACKAGE}}` | {{VERSIONS}} → {{TARGET}} | {{RISK}} | {{IMPACT}} | Dedupe  | {{RATIONALE}} |

## Keep

| Package       | Finding     | Risk     | Impact     | Verdict | Rationale     |
| ------------- | ----------- | -------- | ---------- | ------- | ------------- |
| `{{PACKAGE}}` | {{FINDING}} | {{RISK}} | {{IMPACT}} | Keep    | {{RATIONALE}} |

## Stale Configuration

| File       | Entry       | Action     | Rationale     |
| ---------- | ----------- | ---------- | ------------- |
| `{{FILE}}` | `{{ENTRY}}` | {{ACTION}} | {{RATIONALE}} |

## Decisions

1. **`{{PACKAGE}}`:** {{QUESTION}} Default: {{BEHAVIOR_PRESERVING_OPTION}}.

## Applied Changes

1. {{COMMAND_OR_EDIT}}

{{CHECK_RESULTS}}
