<!-- PMD:START -->

# Plain Markdown Development workflow

PMD uses Markdown and Git as durable state. Coordinator is the user-facing entrypoint and guides work through:

`Task creation → Build → Review → User validation → Approval → Archive → Next task`

The workflow is incremental. A whole PRD does not need to become specs or tasks at once.

## Documentation

```text
docs/
├── agent-policy.md
├── inbox.md
├── changelog.md
├── prd/
├── specs/
└── tasks/
    ├── current/
    └── archived/
```

- **PRDs** describe product intent, user problems, major requirements, and constraints. Change them only when intended requirements change.
- **Specs** describe currently expected behaviour. Meaningful product behaviour normally requires a spec; small bugs, research, refactors, and maintenance may go directly into a task. Every spec creation, edit, rename, or deletion requires explicit user approval.
- **Inbox** entries are uncommitted ideas, bugs, questions, and risks. Remove one only after it is represented elsewhere or explicitly rejected.
- **Changelog** entries describe user-visible completed outcomes and reference their archived task. Add them only during approved task completion.

Active tasks live in `docs/tasks/current/`; completed tasks live in `docs/tasks/archived/`. Use `task-NNN-short-name.md` and preserve the filename during archival. Choose a new number higher than every historical `task-NNN-*` or `iteration-NNN-*` file. Archived historical iteration files remain untouched.

Every new task uses Status `Open`, `Awaiting approval`, or `Completed` and exactly this schema:

```markdown
# Task NNN — Short name

Status: Open
Builder tier: simple | complex
Sources: <paths, or None>
Depends on: <archived task paths, or None>

## Outcome

<one coherent delivered outcome>

## Acceptance

- <observable condition>

## Plan

Pending

## Validation

Pending

## Review

Pending

## Manual validation

None
```

Acceptance items are plain bullets, not nested tasks or checkboxes. Dependencies exist only between task files.

One task must be suitable for one Builder context, one `simple` or `complex` tier, one complete-diff review, one completion approval, and one final commit. Split work when it has independently deliverable outcomes, needs different tiers or approval boundaries, or cannot be implemented and reviewed confidently as one unit. If inseparable work has mixed difficulty, use the stronger Builder for the whole task.

## Roles and decisions

- **Coordinator** creates and prioritizes tasks; owns Outcome, Acceptance, Sources, Depends on, Builder tier, Review, Manual validation, Status, user interaction, and lifecycle routing. It does not design or implement the technical solution.
- **Builder** plans, implements, tests, and directly validates the whole task in one context. In the task file it may edit only Plan and Validation. It does not perform the independent simplification review, stage files, or create commits.
- **Reviewer** independently reviews the complete task diff for correctness, validation, scope, maintainability, and simplification. It is read-only, requires only materially beneficial corrections, and omits non-actionable nits.

Every PMD project requires `docs/agent-policy.md`. Builder and Reviewer return blockers and protected decisions to Coordinator; only Coordinator normally interacts with the user.

Coordinator advances through procedural handoffs without asking when no approval, protected decision, ambiguous choice, or user-run validation is required. Never require the user to name another PMD skill.

## Required gates

Use this source priority when documents conflict:

1. Explicit current user instruction
2. PRD
3. Spec
4. Current task
5. Existing implementation
6. Inbox entry

Do not silently resolve meaningful contradictions. A spec change always requires explicit user approval, even when another source suggests different behaviour.

Coordinator sets `Awaiting approval` only after:

- Builder implementation and direct validation succeed
- Reviewer returns `PASS` for the complete current diff, including its mandatory simplification review
- every required user-run validation passes
- acceptance criteria and affected documentation are satisfied
- no protected decision remains unresolved

Corrections return to the same Builder context and tier when possible, followed by fresh validation and another complete-diff review. A task that needs another tier or is too large returns to Coordinator for reassignment or splitting.

After Reviewer `PASS`, Coordinator records an auditable baseline covering the entire reviewed diff and task contract, including all reviewed files and initially untracked changes. The task path, title, tier, Sources, Depends on, Outcome, Acceptance, Plan, Validation, and the entire reviewed diff must remain unchanged. Only Coordinator gate-recording updates to Review, Manual validation, and Status are exempt; any other task-owned change requires fresh review. If the baseline cannot establish this at completion, obtain a fresh review.

Completion has a separate approval gate. `pmd-complete` Stage 1 verifies integrity and readiness without repeating implementation review, presents a fresh report, and explicitly asks whether to complete and archive the task. General consent to continue or Reviewer `PASS` is not archive approval.

Only approved Stage 2 may update `docs/changelog.md`, set `Completed`, move the task unchanged to `docs/tasks/archived/`, stage task files, and create the isolated final task commit. No earlier role or stage creates a task commit. The commit is its last repository mutation.

## Skill routing

- Use `pmd-setup` to initialize or finish configuring PMD.
- Use `pmd-update` to update an existing installation without changing project documentation.
- Use `pmd-coordinate` for all user-facing task creation, implementation, review, completion, and continuation requests.
- Coordinator delegates implementation planning, implementation, and direct validation to `pmd-builder`; independent review to `pmd-review`; and approved completion to `pmd-complete`.
- Builder, Reviewer, and Complete return control to Coordinator; they do not choose the next lifecycle stage.

After setup or update, restart the agent or start a new session so it reloads the installed skills and instructions.

<!-- PMD:END -->
