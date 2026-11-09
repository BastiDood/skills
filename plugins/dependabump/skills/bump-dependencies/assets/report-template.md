# Dependency Bump Report

{{SCOPE_SUMMARY}}

## Safe

| Members                      | Version                               | Risk     | Impact     | Verdict | Rationale     |
| ---------------------------- | ------------------------------------- | -------- | ---------- | ------- | ------------- |
| `{{MEMBER}}`<br>`{{MEMBER}}` | `{{OLD_VERSION}}` → `{{NEW_VERSION}}` | {{RISK}} | {{IMPACT}} | Safe    | {{RATIONALE}} |

## Hold

| Members                      | Version                               | Risk     | Impact     | Verdict | Rationale     |
| ---------------------------- | ------------------------------------- | -------- | ---------- | ------- | ------------- |
| `{{MEMBER}}`<br>`{{MEMBER}}` | `{{OLD_VERSION}}` → `{{NEW_VERSION}}` | {{RISK}} | {{IMPACT}} | Hold    | {{RATIONALE}} |

## Decisions

1. **`{{PACKAGE}}`:** {{QUESTION}} Default: {{BEHAVIOR_PRESERVING_OPTION}}.

## Findings

### {{GROUP_NAMES}}

- Dependency changes: {{DEPENDENCY_CHANGES}}
- {{CHANGE}} (`{{VERSION}}`, [source]({{URL}})): {{EFFECT}} at `{{PATH_LINE}}`. Fix: {{FIX}}.

## Compatibility Notes

- {{DEPRECATION_OBSOLETE_WORKAROUND_OR_FUTURE_HAZARD_WITH_SOURCE}}

## Apply Plan

1. Overrides and patches: {{OVERRIDE_AND_PATCH_CHANGES}}
2. Constrain: {{RANGE_REWRITES}}
3. Update: {{UPDATE_COMMANDS}}
4. Edit: {{CODE_CHANGES}}
5. Collapse and verify: {{DUPLICATE_COLLAPSE_AND_CHECKS}}
6. Clean: {{CLEANUP_SCOPE_OR_OPT_OUT}}
