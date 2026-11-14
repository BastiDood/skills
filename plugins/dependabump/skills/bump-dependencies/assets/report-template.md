# Dependency Bump Report

<!-- Use one row per group. List the members and their individual version pairs in matching order. Sort each partition by "Risk", then "Impact", then package name. Omit unused bullets, empty sections, and template comments. -->

<!-- Lead each "Rationale" with the evidence that decides the verdict. Cite the source or local path that supports it. -->

<!-- For "Safe", give the edit or setting and its verification when needed. For "Hold", give the blocker and what would clear it. Include the concrete payoff when it matters to the decision. Give the eligibility time for release-age exclusions. -->

<!-- Report transitive changes in the direct dependency group's row when they affect a direct dependency's behavior or compatibility, the project, or the group's verdict. When resolution decides the verdict, name the locked versions, the dependents and ranges, and the resulting versions. Distinguish projected from verified resolutions. -->

{{SCOPE_SUMMARY}}

## Safe

| Package      | Locked → Target   | Risk/Impact                                            | Rationale     |
| ------------ | ----------------- | ------------------------------------------------------ | ------------- |
| {{PACKAGES}} | {{VERSION_PAIRS}} | **{{RISK}}/{{IMPACT}}.** {{AFFECTED_CODE_OR_BEHAVIOR}} | {{RATIONALE}} |

## Hold

| Package      | Locked → Target   | Risk/Impact                                            | Rationale     |
| ------------ | ----------------- | ------------------------------------------------------ | ------------- |
| {{PACKAGES}} | {{VERSION_PAIRS}} | **{{RISK}}/{{IMPACT}}.** {{AFFECTED_CODE_OR_BEHAVIOR}} | {{RATIONALE}} |

## Decisions

<!-- Ask for decisions that change the proposed work, such as accepting a behavior change, resolving a material evidence gap, or updating an override or patch. State the behavior-preserving default. -->

- **`{{PACKAGE}}`** {{QUESTION_AND_OPTIONS}} {{DEFAULT}}

## Compatibility Notes

- {{SUPPORTING_FINDING_WITH_SOURCE_OR_RELEASE_AGE_EXCLUSION}}

## Apply Plan

<!-- Show the project-specific changes and checks the user is approving. For an ordinary update, show the constraints to apply first, including any range that would otherwise admit a held release. -->

- {{PROJECT_SPECIFIC_CHANGE_OR_CHECK}}
