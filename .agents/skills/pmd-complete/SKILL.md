---
name: pmd-complete
description: Verify that a current iteration is ready to finish, request explicit user approval, and after approval update the changelog, archive the iteration, and commit its completed work. Use when the user asks to review, finish, approve, close, or archive an iteration.
---

# Complete an iteration

Completion is a two-stage workflow:

1. verify and request approval
2. archive and commit only after explicit user approval

Every completion attempt starts with Stage 1. Never treat the request that starts the completion review, even if it asks to approve or archive, as approval to archive. Approval is fresh only when the user gives it after the latest readiness report.

An `Awaiting approval` status or prior Reviewer `PASS` may indicate that implementation gates succeeded, but never replaces the fresh Stage 1 readiness review or the explicit approval required for Stage 2. Do not load runtime configuration or resume orchestration while completing an iteration.

## Stage 1: verify and request approval

1. Read the selected current iteration, referenced specs, relevant PRDs, and implementation.
2. Review every checkbox and verify that checked tasks are actually complete.
3. Run the relevant tests, linters, type checks, builds, and other project checks.
4. Compare implemented behaviour with specs and acceptance criteria.
5. Inspect Git state and identify the exact implementation, test, documentation, and PMD-state changes belonging to this iteration for its final commit. If unrelated staged changes or an ambiguous diff prevent an isolated completion commit, report the problem and resolve it before requesting completion approval.
6. Do not modify a spec without explicit user approval.
7. When a spec appears outdated:
   - explain the mismatch
   - discuss the correct behaviour with the user when needed
   - draft the exact update from that discussion and repository context
   - ask for approval before editing it
8. Resolve unfinished work:
   - treat every checkbox as required and leave unfinished tasks unchecked, or
   - move a task that is no longer needed to a clearly labeled deferred section as a plain list item or to `docs/inbox.md`
9. Add concise completion notes only when useful.
10. If any checkbox remains unchecked, report that the iteration is not ready.
11. If ready, set status to `Awaiting approval`.
12. Present a completion summary and ask concisely whether the user approves completing and archiving the iteration. Use an available question tool when practical; otherwise request a short yes/no answer. Do not require the user to invoke or name `pmd-complete`.

Do not update the changelog or move the file during Stage 1.

## Stage 2: archive after explicit approval

Proceed only when the user explicitly approves completing or archiving this iteration after the latest readiness report.

1. Re-read the iteration and confirm it is ready.
2. Confirm there are no unchecked checkboxes.
3. Before changing any file, confirm all of the following:
   - the iteration still exists in `docs/tasks/current/`
   - its status is `Awaiting approval`
   - the destination does not already exist in `docs/tasks/archived/`
   - `docs/changelog.md` has no entry or archived-path reference for this iteration
   - the iteration-related commit scope identified in Stage 1 is still reliable and will not include unrelated staged changes
4. If any preflight check fails, stop without making changes and report the conflicting state. Never overwrite an archived file or duplicate a changelog entry.
5. Add one user-visible entry to `docs/changelog.md`.
6. Reference the archived iteration path:

   `docs/tasks/archived/iteration-NNN-short-name.md`

7. Set the iteration status to `Completed`.
8. Move the file from `docs/tasks/current/` to `docs/tasks/archived/` without renaming it.
9. Stage exactly the implementation, tests, documentation, changelog, and iteration move belonging to the completed iteration. Inspect the staged diff and exclude unrelated work.
10. Create a final commit with a concise project-appropriate message such as `Complete iteration NNN short name`. The commit is the last repository-changing action in the completion workflow.
11. If staging or committing fails, stop and report the exact uncommitted completion state. Do not continue to another iteration until the completion commit succeeds.
12. Report the archived path, changelog update, and commit.
13. Return control to Coordinator so it can continue with the next clear current iteration or invoke `pmd-plan`.

## Changelog format

```markdown
## YYYY-MM-DD — Iteration NNN

Iteration: `docs/tasks/archived/iteration-NNN-short-name.md`

- <User-visible completed outcome>
- <User-visible completed outcome>
```

The changelog records outcomes, not individual engineering steps. Do not include speculative, deferred, or unfinished work.

The completion commit may include implementation changes that were not checkpointed earlier, but it must contain only work belonging to the approved iteration. Never include unrelated staged or working-tree changes merely to make the tree clean.
