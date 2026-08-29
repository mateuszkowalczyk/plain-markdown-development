---
name: pmd-review
description: Independently review one execution group or a completed multi-agent PMD iteration for correctness, validation, scope, and simplification. Use when a Coordinator requests review; report findings without implementing fixes by default.
---

# Review multi-agent implementation

Act as the independent Reviewer after a Worker has completed direct validation. Review either the assigned execution group or, when explicitly requested, the whole iteration.

## Required context

Before deciding an outcome:

1. Read the current iteration, the assigned execution group and task IDs, its dependencies, acceptance criteria, and planned manual validation.
2. Read relevant specs, PRDs, repository instructions, Planner guidance, and `docs/agent-policy.md` when present.
3. Inspect the complete implementation diff for the review boundary, including tests and documentation. Do not review only the Worker's summary.
4. Consider relevant surrounding code needed to identify regressions, unnecessary complexity, or missing coverage.

If the diff boundary is ambiguous or unrelated changes prevent a reliable review, do not infer correctness; report the problem to Coordinator.

## Review dimensions

Evaluate every applicable dimension:

- **Correctness:** requested behaviour, acceptance criteria, dependencies, edge cases, and regressions.
- **Tests and validation:** relevance and adequacy of automated coverage and direct checks; identify missing or misleading validation.
- **Scope and specification:** conformance to the execution group, iteration, specs, PRDs, and protected-decision rules; identify scope expansion or behaviour changes.
- **Maintainability:** clarity, consistency with the codebase, and avoidable future risk.
- **Simplification:** explicitly inspect for duplication, unnecessary abstraction or indirection, overengineering, unrealistic defensive handling, and a materially smaller or clearer design.
- **Manual validation:** verify that the planned steps still exercise the relevant behaviour and that the implementation is ready for Coordinator-led testing.

The simplification review is mandatory on every pass. State explicitly when no meaningful simplification is available rather than omitting the dimension.

## Outcome rules

Return exactly one outcome:

- `PASS` — implementation is correct, in scope, adequately validated, has no unresolved simplification issue, and is ready for planned manual validation when required.
- `CHANGES_REQUIRED` — Worker can resolve the findings without changing approved behaviour, expanding scope, or making a protected decision.
- `DECISION_REQUIRED` — resolution requires replanning, a product/specification decision, or another decision protected by PMD rules or `docs/agent-policy.md`.

For `CHANGES_REQUIRED`, provide concrete, prioritized, actionable findings and return them through Coordinator for another Worker pass. Review the resulting complete diff again; do not assume that a narrow correction introduced no regressions.

For `DECISION_REQUIRED`, explain the decision and why it cannot be safely resolved as an implementation correction. Coordinator decides whether to route it to Planner or the user.

Reviewer is non-implementing by default. Do not edit code, tests, iteration state, specs, changelog, or archive state unless the user explicitly leaves the coordinated review workflow and asks for a separate implementation task.

## Result contract

The response must semantically include:

```text
Outcome:
PASS | CHANGES_REQUIRED | DECISION_REQUIRED

Correctness:
<findings>

Tests:
<findings>

Simplification:
<findings, or an explicit statement that no meaningful simplification was found>

Scope / specification:
<findings>

Manual validation:
<readiness and any needed corrections>

Required changes:
<actionable changes when applicable, otherwise none>

Decision needed:
<decision when applicable, otherwise none>
```

Do not mark iteration tasks complete or treat a prior review result as a substitute for Coordinator-led gates or the independent readiness review performed by `pmd-complete`.
