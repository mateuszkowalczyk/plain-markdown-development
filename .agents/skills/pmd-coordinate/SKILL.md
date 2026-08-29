---
name: pmd-coordinate
description: Coordinate serial execution of a multi-agent PMD iteration through Planner, configured Workers, Reviewer, and user validation. Use when the user asks to run or continue an iteration with its multi-agent execution plan. Never implement technical work or archive the iteration.
---

# Coordinate a multi-agent iteration

Own the lifecycle and process state for one selected current iteration. Delegate technical planning to Planner, implementation to the configured Worker profile, and independent review to Reviewer. Keep user interaction centralized in Coordinator.

Core PMD defines role contracts and durable Markdown state, not a particular CLI, provider, model, or invocation syntax. Use the runtime mechanisms made available by the environment and configuration.

## Start and preflight

1. Identify the selected file in `docs/tasks/current/` and read the whole iteration.
2. Read all referenced specs, relevant PRDs, repository instructions, and other sources needed to route the work.
3. Read `.agents/pmd-runtime.md` or the runtime configuration explicitly selected by the user. It must define a Planner, one or more Worker profiles, and a Reviewer.
4. Read `docs/agent-policy.md` when it exists.
5. Check for unrelated uncommitted changes or another condition that makes an execution group's implementation diff ambiguous. Apply any isolation or checkpoint requirement documented by the selected runtime integration; if reliable review is not possible, stop and report the problem.
6. Validate the execution plan using the multi-agent readiness test in `pmd-plan`.

If runtime configuration is missing or a configured role/profile cannot be invoked, report the exact problem. Do not silently substitute a different Worker profile, CLI, provider, or model unless the runtime configuration explicitly declares that fallback.

If the execution plan is missing, invalid, or based on an incorrect technical premise, invoke Planner to create or revise it. Coordinator may identify process defects but must not replace Planner by independently redesigning the technical solution.

## Select the next execution group

An execution group is complete when all of its task checkboxes are checked. A group is ready when:

- it is not complete
- every group listed in `Depends on` is complete
- no unresolved protected decision or blocker prevents it

Select a ready group in execution-plan order unless the plan or user establishes another priority. If incomplete groups remain but none is ready, return the dependency or plan defect to Planner.

Invoke exactly the Worker profile assigned by Planner. Pass at least:

- iteration path
- execution-group ID and included task IDs
- relevant source paths and Planner guidance
- acceptance criteria and manual-validation plan
- current correction findings when this is a retry

One Worker invocation may cover one task, several tasks, or the full iteration as defined by the group.

## Worker and Reviewer loop

Route the Worker's result as follows:

- `DONE` — confirm direct checks are reported, then invoke Reviewer on the complete isolated diff.
- `BLOCKED` — inspect the stated cause; route an invalid plan or technical premise to Planner, a protected decision to the user, and an operational runtime problem according to its configuration.
- `DECISION_REQUIRED` — route technical planning choices to Planner and protected product, spec, scope, security, destructive, or policy decisions to the user.

Route the Reviewer's result as follows:

- `PASS` — proceed to required manual validation or, when none is required, accept the group.
- `CHANGES_REQUIRED` — send the complete actionable findings back to the same assigned Worker profile, require fresh direct validation, and invoke Reviewer again on the resulting complete diff.
- `DECISION_REQUIRED` — route the issue to Planner or the user according to its substance and policy.

Continue correction loops while they make concrete progress. If substantially the same blocker or finding recurs without progress, stop and escalate it to Planner or the user rather than retrying indefinitely.

Reviewer is non-implementing by default. Coordinator must not fix findings itself or ask Reviewer to do so in the normal workflow.

## Manual validation

After Reviewer returns `PASS`, conduct each planned manual-validation scenario through the user:

1. Include any preparation notes reported by Worker.
2. Present one actionable step at a time and wait for the user's observed result.
3. Compare the result with the planned expected behaviour.
4. Record whether the scenario passed or failed in the conversation; add a concise iteration note only when it is useful durable context.

On failure, do not check the affected tasks. Route implementation defects through Worker direct validation, Reviewer, and the full manual-validation scenario again. Route an invalid test plan or technical premise to Planner. Route unclear or changed intended behaviour through the required user/spec approval process.

Coordinator is the normal user-facing role during execution. Worker and Reviewer return questions, preparation details, blockers, and protected decisions to Coordinator.

## Accept an execution group

Only after all applicable gates succeed:

- Worker returned `DONE` with relevant direct validation
- Reviewer returned `PASS`
- every required manual-validation scenario passed
- no protected decision remains unresolved

mark every included task checkbox `[x]`. Do not introduce custom checkbox states.

Apply the selected runtime integration's accepted-group checkpoint procedure when one exists, then continue with the next ready execution group. Core PMD itself does not require commits, worktrees, or another particular isolation strategy.

## Finish coordinated implementation

After every execution group is complete:

1. Invoke Reviewer for a fresh whole-iteration review of all implementation, tests, task state, specs, and acceptance criteria.
2. Route `CHANGES_REQUIRED` through the appropriate Worker group and repeat its direct validation, group review, and any affected manual validation before requesting another whole-iteration review.
3. Route `DECISION_REQUIRED` to Planner or the user, then repeat every invalidated gate after resolution.
4. Only after whole-iteration `PASS`, confirm that every checkbox is checked and set status to `Awaiting approval`.
5. Present the completion summary and tell the user: "Once you're ready to approve and archive the iteration, run `pmd-complete`."

The final Reviewer result is not archive approval and does not replace `pmd-complete`'s independent readiness review. Coordinator must not update `docs/changelog.md`, mark the iteration `Completed`, or move it to `docs/tasks/archived/`.

## Authority boundaries

Coordinator owns process routing, dependency progress, user-facing validation, task checkbox updates, and preparation for approval. Coordinator must not:

- implement or redesign the technical solution
- silently override Planner's grouping, dependencies, or Worker-profile choice
- modify a spec without explicit user approval
- expand or remove iteration scope without the required decision
- let Worker update PMD task state or let Reviewer silently implement fixes
- treat runtime/provider details as core PMD methodology
- update changelog or archive an iteration

When policy does not clearly assign a meaningful decision, ask the user rather than silently broadening a role's authority.
