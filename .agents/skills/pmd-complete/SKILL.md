---
name: pmd-complete
description: Verify that a current iteration is ready to finish, request explicit user approval, and after approval update the changelog and archive that iteration. Use when the user asks to review, finish, approve, close, or archive an iteration.
---

# Complete an iteration

Completion is a two-stage workflow:

1. verify and request approval
2. archive only after explicit user approval

Never treat a request to review readiness as approval to archive.

## Stage 1: verify and request approval

1. Read the selected current iteration, referenced specs, relevant PRDs, and implementation.
2. Review every checkbox and verify that checked tasks are actually complete.
3. Run the relevant tests, linters, type checks, builds, and other project checks.
4. Compare implemented behaviour with specs and acceptance criteria.
5. Do not modify a spec without explicit user approval.
6. When a spec appears outdated:
   - explain the mismatch
   - discuss the correct behaviour with the user when needed
   - draft the exact update from that discussion and repository context
   - ask for approval before editing it
7. Resolve unfinished work:
   - treat every checkbox as required and leave unfinished tasks unchecked, or
   - move a task that is no longer needed to a clearly labeled deferred section as a plain list item or to `docs/inbox.md`
8. Add concise completion notes only when useful.
9. If any checkbox remains unchecked, report that the iteration is not ready.
10. If ready, set status to `Awaiting approval`.
11. Present a completion summary and explicitly ask the user to approve completion.

Do not update the changelog or move the file during Stage 1.

## Stage 2: archive after explicit approval

Proceed only when the user explicitly approves completing or archiving this iteration.

1. Re-read the iteration and confirm it is ready.
2. Confirm there are no unchecked checkboxes.
3. Add one user-visible entry to `docs/changelog.md`.
4. Reference the archived iteration path:

   `docs/tasks/archived/iteration-NNN-short-name.md`

5. Set the iteration status to `Completed`.
6. Move the file from `docs/tasks/current/` to `docs/tasks/archived/` without renaming it.
7. Report the archived path and changelog update.
8. Suggest the next step based on the remaining work:
   - use `pmd-implement` when another current iteration is ready to implement
   - otherwise use `pmd-plan` to plan the next iteration

## Changelog format

```markdown
## YYYY-MM-DD — Iteration NNN

Iteration: `docs/tasks/archived/iteration-NNN-short-name.md`

- <User-visible completed outcome>
- <User-visible completed outcome>
```

The changelog records outcomes, not individual engineering steps. Do not include speculative, deferred, or unfinished work.
