---
name: pmd-implement
description: Implement all or a user-selected part of a current Markdown iteration while keeping task checkboxes, tests, and documentation consistent. Use when the user asks to implement an iteration, a task group, specific tasks, or the next unchecked task. May prepare the iteration for approval, but never archives it.
---

# Implement iteration tasks

Implement only the portion of an iteration requested by the user.

## Supported scopes

The user may ask to implement:

- the entire iteration
- one task group
- specific named tasks
- the next unchecked task
- all remaining tasks
- any other clearly identified subset

Do not expand the requested scope without explaining why it is necessary.

## Procedure

1. Locate the relevant file in `docs/tasks/current/`.
2. Read all referenced specs and relevant PRD sections before changing code.
3. Select tasks strictly according to the user's instruction.
4. Implement the smallest coherent set of changes needed.
5. Add or update tests that verify the acceptance criteria.
6. Run relevant tests, linters, type checks, and build checks.
7. Mark a checkbox `[x]` only when:
   - implementation is complete
   - relevant checks pass
   - acceptance criteria are met
   - affected documentation is consistent
8. Leave incomplete and unselected tasks unchecked.
9. Add concise implementation notes only when useful.
10. Do not update a spec merely because implementation is complete.
11. If expected behaviour needs to be clarified or changed:
   - discuss the behaviour with the user
   - draft the proposed spec update from that discussion and repository context
   - obtain explicit spec-change approval from the user
   - only then modify the spec
12. Do not update `docs/changelog.md`.
13. Report:
   - tasks completed
   - tasks still open
   - checks run and results
   - deviations, blockers, or proposed documentation changes
14. Suggest the next step:
   - when the requested work covered only part of the iteration, suggest using `pmd-implement` to implement another part
   - when any checkbox remains unchecked, suggest using `pmd-implement` to continue it
   - when all checkboxes are checked and validation passes, tell the user: "Once you're ready to approve and archive the iteration, run `pmd-complete`."

## Preparing completion automatically

After implementation, inspect the whole iteration.

When all checkboxes are checked and validation passes:

1. add concise completion notes only when useful
2. set the iteration status to `Awaiting approval`
3. present a completion summary
4. tell the user: "Once you're ready to approve and archive the iteration, run `pmd-complete`."

Explicit spec-change approval is still required before modifying a spec during implementation. Do not request iteration-completion approval, including approval to archive, during implementation; that separate gate belongs to `pmd-complete`. Do not archive the iteration, update the changelog, or mark it `Completed`.

## Scope discipline

Do not opportunistically implement unrelated inbox items or deferred work.

Every checkbox is required for iteration completion. If a task is no longer needed, move it to a clearly labeled deferred section as a plain list item or to `docs/inbox.md`.

When implementation reveals new work:

- add a required blocker to the current iteration only when necessary to complete selected scope
- otherwise append a concise item to `docs/inbox.md`
- explain the decision
