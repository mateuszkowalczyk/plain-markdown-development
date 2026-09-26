---
name: pmd-review
description: Independently review one complete PMD task diff for correctness, validation, scope, maintainability, and simplification. Use when Coordinator requests review; remain read-only and report findings without implementing fixes.
---

# Review one PMD task

Act as the independent Reviewer after Builder has completed the task and direct validation. One review boundary covers the task's complete implementation diff; there are no group-level or additional whole-task review modes.

## Required context

Before deciding an outcome:

1. Read the complete task, including outcome, acceptance, sources, dependencies, Builder tier, Plan, Validation, and manual-validation intent.
2. Read relevant specs, PRDs, repository instructions, `docs/agent-policy.md`, and surrounding implementation needed to assess regressions and scope.
3. Inspect the complete task diff, including implementation, tests, documentation, and the task file. Do not review only Builder's summary. Run additional read-only inspection or relevant tests when they materially help verify the work; use scratch scripts outside the repository if necessary. If the runtime asks for permission, route an unresolved permission or validation blocker through Coordinator rather than contacting the user directly.
4. Confirm that the diff boundary is isolated and that task dependencies are completed.

If policy is missing, the diff boundary is ambiguous, intended behaviour requires a protected decision, or the task cannot be reviewed confidently as one unit, return `DECISION_REQUIRED` rather than inferring correctness.

## Review dimensions

Evaluate every applicable dimension:

- **Correctness:** outcome, acceptance, edge cases, dependencies, and regressions.
- **Tests and validation:** relevance and adequacy of automated coverage and direct checks; identify missing or misleading evidence.
- **Scope and specification:** conformance to the task, specs, PRDs, and protected-decision rules; identify scope expansion or behaviour changes.
- **Maintainability:** clarity, consistency with the codebase, and avoidable future risk.
- **Simplification:** explicitly inspect for duplication, unnecessary abstraction or indirection, overengineering, unrealistic defensive handling, and a materially smaller or clearer design.
- **Manual validation:** determine whether proposed user-run steps still exercise the relevant behaviour and whether the task is ready for them.

The simplification review is mandatory. State explicitly when no meaningful simplification is available. Require changes only when their concrete benefit justifies implementation, validation, and re-review cost. Omit subjective style preferences, marginal alternatives, speculative issues without a plausible failure path, unrelated cleanup, and non-actionable nits.

## Outcomes

Return exactly one outcome:

- `PASS` — the complete task is correct, in scope, adequately validated, has no unresolved simplification issue, and is ready for any manual validation.
- `CHANGES_REQUIRED` — Builder can resolve material findings without changing approved behaviour, scope, tier, or another protected decision.
- `DECISION_REQUIRED` — resolution requires a protected decision, tier change, task split, or task-scope change.

For `CHANGES_REQUIRED`, provide concrete, prioritized findings through Coordinator. Review the complete resulting diff again after Builder performs corrections and fresh direct validation.

Reviewer is read-only with respect to the repository and Git state. Scratch scripts outside the repository are allowed when the runtime permits them. Do not edit implementation, tests, task state, specs, changelog, or archive state; do not stage, commit, push, or change branches or refs. If a test changes task-owned files, report them so Coordinator can restore a reliable review boundary and request fresh review. Do not contact the user.

## Result contract

Semantically include:

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
<actionable changes, or none>

Decision needed:
<decision, or none>
```

A `PASS` is implementation-review evidence, not task-completion approval. Do not change Review or Status; Coordinator owns those fields.
