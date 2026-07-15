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

1. Read `AGENTS.md`.
2. Locate the relevant file in `docs/tasks/current/`.
3. Read all referenced specs and relevant PRD sections before changing code.
4. Select tasks strictly according to the user's instruction.
5. Implement the smallest coherent set of changes needed.
6. Add or update tests that verify the acceptance criteria.
7. Run relevant tests, linters, type checks, and build checks.
8. Mark a checkbox `[x]` only when:
   - implementation is complete
   - relevant checks pass
   - acceptance criteria are met
   - affected documentation is consistent
9. Leave incomplete and unselected tasks unchecked.
10. Add concise implementation notes only when useful.
11. Do not update a spec merely because implementation is complete.
12. If expected behaviour needs to be clarified or changed:
   - discuss the behaviour with the user
   - draft the proposed spec update from that discussion and repository context
   - obtain explicit user approval
   - only then modify the spec
13. Do not update `docs/changelog.md`.
14. Report:
   - tasks completed
   - tasks still open
   - checks run and results
   - deviations, blockers, or proposed documentation changes
15. Suggest the next step:
   - when the requested work covered only part of the iteration, suggest using `pmd-implement` to implement another part
   - when required work remains incomplete, suggest using `pmd-implement` to continue it
   - when all required tasks are checked and validation passes, tell the user: "Once you're ready to approve and archive the iteration, run `pmd-complete`."

## Preparing completion automatically

After implementation, inspect the whole iteration.

When all required tasks are checked and validation passes:

1. add concise completion notes only when useful
2. set the iteration status to `Awaiting approval`
3. present a completion summary
4. tell the user: "Once you're ready to approve and archive the iteration, run `pmd-complete`."

Do not ask for approval during implementation. Do not archive the iteration, update the changelog, or mark it `Completed`; those steps belong to `pmd-complete`.

## Scope discipline

Do not opportunistically implement unrelated inbox items or deferred work.

When implementation reveals new work:

- add a required blocker to the current iteration only when necessary to complete selected scope
- otherwise append a concise item to `docs/inbox.md`
- explain the decision
