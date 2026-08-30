<!-- PMD:START -->

# Plain Markdown Development workflow

PMD uses Markdown and Git as durable state. Coordinator is the user-facing entrypoint and guides work through:

`Requirements → Planning → Implementation → Review → User validation → Approval → Archive → Next iteration`

The workflow is incremental; a whole PRD does not need to become specs or tasks at once.

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
- **Specs** describe currently expected behaviour. Meaningful product behaviour normally requires a spec; small bugs, research, refactors, and maintenance may go directly into an iteration. Every spec creation, edit, rename, or deletion requires explicit user approval.
- **Inbox** entries are uncommitted ideas, bugs, questions, and risks. Remove one only after it is represented elsewhere or explicitly rejected.
- **Changelog** entries describe user-visible completed outcomes and reference their archived iteration. Add them only during approved iteration completion.

Active iterations live in `docs/tasks/current/`; completed iterations live in `docs/tasks/archived/`. Use `iteration-NNN-short-name.md` without renaming it during archival. Multiple current iterations are allowed.

Iteration statuses are `Planned`, `Awaiting approval`, and `Completed`. Use only `[ ]` and `[x]` task checkboxes. Every checkbox is required for completion; record deferred work as plain list items or return it to the inbox.

Every iteration contains a title, status, applicable sources, stable task IDs, and an execution plan. Each task belongs to exactly one stable execution group. A group may contain one or more tasks, depend on other groups, and names exactly one Worker profile from `.agents/pmd-runtime.md`. Runtime configuration owns CLI, provider, model, invocation, and fallback mappings.

## Roles and decisions

- **Coordinator** owns lifecycle routing, user interaction, manual validation, task state, completion handoffs, and continuation across iterations. It does not design or implement the technical solution.
- **Planner** owns technical planning, task decomposition, execution groups, dependencies, acceptance criteria, manual-validation design, and Worker-profile assignment. It never implements code.
- **Worker** implements and directly validates only its assigned execution group. It never updates PMD task state or performs the separate PMD simplification review.
- **Reviewer** independently reviews correctness, validation, scope, maintainability, and simplification. It reports findings and does not implement fixes by default.

Every PMD project requires `docs/agent-policy.md`. It assigns project-specific decisions to the user, Planner, or Worker. Worker and Reviewer return blockers and protected decisions to Coordinator; only Coordinator normally interacts with the user.

Coordinator advances through procedural handoffs without asking when no approval, protected decision, ambiguous choice, or user-run validation is required. When input is necessary, ask for the decision or observable result itself, preferably with a question tool or concise short-answer prompt. Never require the user to name another PMD skill.

## Required gates

Use this source priority when documents conflict:

1. Explicit current user instruction
2. PRD
3. Spec
4. Current iteration
5. Existing implementation
6. Inbox entry

Do not silently resolve meaningful contradictions. A spec change always requires explicit user approval, even when another source suggests different behaviour.

Coordinator marks a task complete only after:

- Worker implementation and direct validation succeed
- Reviewer returns `PASS`, including its mandatory simplification review
- every required user-run validation passes
- acceptance criteria and affected documentation are satisfied
- no protected decision remains unresolved

After every group is accepted, Reviewer performs a fresh whole-iteration review. Coordinator sets `Awaiting approval` only after that review passes, then invokes `pmd-complete` Stage 1 automatically.

Completion has a separate approval gate. Stage 1 performs a fresh readiness review and asks explicitly whether to complete and archive the iteration. The request that started review, general consent to continue, or a prior Reviewer `PASS` is not archive approval.

Only approved `pmd-complete` Stage 2 may update `docs/changelog.md`, set `Completed`, and move the iteration to `docs/tasks/archived/`. It must reject unchecked tasks, an existing destination, a duplicate changelog reference, or an ambiguous commit scope. Its final repository mutation is an isolated iteration commit; after that commit, control returns to Coordinator.

## Skill routing

- Use `pmd-setup` to initialize or finish configuring PMD.
- Use `pmd-update` to update an existing installation without changing project documentation.
- Use `pmd-coordinate` for all user-facing planning, implementation, review, completion, and continuation requests.
- Coordinator delegates planning to `pmd-plan`, implementation to `pmd-worker`, independent review to `pmd-review`, and approved completion to `pmd-complete`.
- Planner, Worker, Reviewer, and Complete return control to Coordinator; they do not choose the next lifecycle stage themselves.

After setup or update, restart the agent or start a new session so it reloads the installed skills and instructions.

<!-- PMD:END -->
