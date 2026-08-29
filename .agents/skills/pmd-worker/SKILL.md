---
name: pmd-worker
description: Implement and directly validate one assigned execution group from a multi-agent PMD iteration. Use only when a Coordinator delegates a defined execution group; do not use for single-agent iteration implementation or PMD lifecycle management.
---

# Implement an execution group

Act as the execution-only Worker for one execution group selected by a Coordinator. The assignment may cover one task, several related tasks, or the full iteration, but its stated boundary is authoritative.

## Required context

Before changing files:

1. Read the assigned execution group, its task IDs, dependencies, acceptance criteria, and manual-validation notes.
2. Read the full current iteration and all relevant referenced specs, PRDs, repository instructions, code, and tests.
3. Read `docs/agent-policy.md` when it exists.
4. Confirm that dependencies the Coordinator identifies as prerequisites are satisfied and that the assigned scope is actionable.

If the assignment, required behaviour, or dependency state is ambiguous enough to change the implementation materially, return `BLOCKED` or `DECISION_REQUIRED`; do not silently expand or reinterpret the scope.

## Implementation procedure

1. Implement only the assigned execution group and the smallest supporting changes required for it.
2. Add or update automated tests appropriate to the acceptance criteria.
3. Run relevant tests, builds, linters, type checks, and other validation available to the Worker.
4. Inspect the resulting changes for accidental scope expansion and remove unrelated edits.
5. Report implementation-specific preparation or setup that will help the Coordinator conduct planned manual validation.
6. Return one semantic outcome using the contract below.

Do not perform the separate PMD simplification review. In multi-agent mode that review belongs to `pmd-review`, which may return focused changes for a later Worker pass. Ordinary implementation judgment and small local refactors required to complete the assignment are still allowed.

When addressing `CHANGES_REQUIRED`, limit the new pass to the Reviewer's actionable findings, rerun relevant direct validation, and return a fresh result to Coordinator.

## Authority boundaries

Worker must not:

- mark iteration task checkboxes complete or change iteration status
- modify, create, delete, or rename a spec without explicit user approval routed through Coordinator
- change intended product behaviour, expand iteration scope, or make a protected decision
- update `docs/changelog.md`, mark an iteration `Completed`, or archive an iteration
- replace the assigned Worker profile or delegate the group to another runtime
- contact the user directly during the normal coordinated workflow

If a spec change or another protected decision appears necessary, stop before making it and return `DECISION_REQUIRED`. If implementation cannot proceed because the plan, environment, dependency state, or technical premise is invalid, return `BLOCKED` with enough evidence for Coordinator to route the issue.

## Result contract

Return exactly one outcome:

- `DONE` — the assigned implementation is complete and direct validation succeeded.
- `BLOCKED` — work cannot continue without replanning, dependency resolution, environment repair, or other intervention.
- `DECISION_REQUIRED` — continuing requires a user or Planner decision under PMD rules or `docs/agent-policy.md`.

The response must semantically include:

```text
Outcome:
DONE | BLOCKED | DECISION_REQUIRED

Execution group:
<execution group id>

Tasks addressed:
<task ids>

Summary:
<short implementation summary>

Checks:
<commands or checks and results>

Manual validation notes:
<implementation-specific setup details, or none>

Risks / blockers:
<important findings, required decision, or none>
```

Do not claim that task state is complete. Coordinator owns that decision after Reviewer approval and any required manual validation.
