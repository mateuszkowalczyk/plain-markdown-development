---
name: pmd-complete
description: Check a reviewed PMD task's readiness, request explicit approval, and only after approval update the changelog, archive the task, and create its final commit. Use only when Coordinator invokes completion after review and any manual validation.
---

# Complete one PMD task

Completion is a two-stage workflow:

1. integrity and readiness check followed by an approval request
2. archive and commit only after explicit user approval

Every completion attempt starts with Stage 1. A request to review, finish, continue, or invoke this skill is not approval for Stage 2.

## Stage 1: check readiness and request approval

1. Read the current task, its sources, `docs/agent-policy.md`, relevant repository instructions, the latest Reviewer result, manual-validation evidence, and repository state.
2. Confirm that the task uses the required schema, exists at `docs/tasks/current/task-NNN-short-name.md`, and has Status `Awaiting approval`.
3. Confirm that Plan and Validation contain substantive current evidence, Review records a `PASS` for the complete implementation diff, every required manual-validation scenario passed, and no protected decision remains unresolved.
4. Confirm from the recorded review baseline and repository state that the task path and title; Builder tier; Sources; Depends on; Outcome; Acceptance; Plan; Validation; and the complete reviewed implementation, test, and behaviour-documentation diff all remain unchanged since `PASS`. Only Coordinator updates to Review, Manual validation, and Status that record completed gates are exempt; any other post-review change makes the review stale.
5. Inspect the prospective task commit scope and confirm it is isolated from unrelated changes. Confirm that the archived destination does not exist and that `docs/changelog.md` has no entry or archived-path reference for this task.
6. Do not repeat the independent correctness, maintainability, or simplification review. If the existing review is missing, stale, or ambiguous, return to Coordinator for a fresh `pmd-review` pass.
7. Present a concise readiness report covering outcome, validation, review, manual validation, changelog summary, archive path, and commit scope.
8. Ask explicitly whether to complete and archive this task now. Use a question tool when available, otherwise ask a concise yes/no question.

Do not update the changelog, task status, archive path, staging area, or commit during Stage 1.

## Stage 2: archive after explicit approval

Proceed only after explicit approval given in response to the latest readiness report.

1. Re-read the task and repository state. Repeat the Stage 1 integrity checks that could have changed, without repeating implementation review.
2. Stop without mutation if the task is no longer `Awaiting approval`, review evidence is stale, manual validation is incomplete, the archived destination exists, the changelog already references the task, or commit scope is ambiguous.
3. Add one user-visible entry to `docs/changelog.md` using the format below.
4. Set Status to `Completed`.
5. Move the file to `docs/tasks/archived/task-NNN-short-name.md` without renaming it.
6. Stage exactly the implementation, tests, documentation, changelog, and task move belonging to this task. Inspect the staged diff and exclude unrelated work.
7. Create the final commit with a concise message such as `Complete task NNN short name`. This commit is the last repository mutation in the completion workflow.
8. If staging or committing fails, stop and report the exact uncommitted state. Do not continue to another task until the final commit succeeds.
9. Report the archived path, changelog update, and commit, then return control to Coordinator.

## Changelog format

```markdown
## YYYY-MM-DD — Task NNN

Task: `docs/tasks/archived/task-NNN-short-name.md`

- <user-visible completed outcome>
```

The changelog records outcomes, not engineering steps. Do not include speculative, deferred, or unfinished work. Never include unrelated staged or working-tree changes merely to make the tree clean.
