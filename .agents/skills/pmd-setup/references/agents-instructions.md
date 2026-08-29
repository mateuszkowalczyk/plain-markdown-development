<!-- PMD:START -->

# PRD-driven development workflow

## Documentation structure

```text
docs/
├── inbox.md
├── changelog.md
├── prd/
├── specs/
└── tasks/
    ├── current/
    └── archived/
```

The default single-agent workflow follows this progression:

`PRD → Spec → Iteration tasks → Implementation + simplification → User validation → Approval → Archived iteration`

An optional multi-agent workflow uses:

`PRD → Spec → Planner → Coordinator → Worker → Reviewer → User validation → Approval → Archived iteration`

The progression is incremental. A whole PRD does not need to be converted into specs or tasks at once.

## Document roles

### PRDs

PRDs live in `docs/prd/`.

They describe product intent, user problems, major features, business requirements, constraints, and high-level acceptance criteria.

PRDs are not implementation plans. Update a PRD only when intended product requirements change.

### Specs

Specs live in `docs/specs/`.

Each spec describes one coherent product capability or system behaviour. Specs describe the currently expected behaviour, not implementation history.

Specs are usually drafted by the agent during iteration planning, based on the product requirements, repository context, and discussion with the user. The user does not need to prepare specs manually.

Specs are required for meaningful product behaviour, but not for every small bug, research task, refactor, or maintenance item.

The agent should discuss unclear behaviour with the user and propose the resulting spec. In any workflow, always obtain explicit spec-change approval from the user before creating, editing, renaming, or deleting a spec.

### Inbox

`docs/inbox.md` is a flat list of loose ideas, bugs, observations, questions, risks, and possible future work.

Inbox entries are possibilities, not requirements or commitments. Do not automatically convert or expand them unless asked.

Remove an inbox entry only after it has been fully represented elsewhere or explicitly rejected.

### Iterations

Active iteration files live in `docs/tasks/current/`.

Completed iteration files live in `docs/tasks/archived/`.

Multiple current iterations may exist at the same time.

Each iteration uses:

`iteration-NNN-short-name.md`

An iteration can contain related or unrelated features, parts of larger features, bugs, technical work, or research.

Task checkboxes may be grouped by feature, work type, subsystem, or any other structure that makes the iteration easy to understand. Bugs may be kept separately or grouped with the affected feature.

Iteration statuses are:

- `Planned`
- `Awaiting approval`
- `Completed`

Checkboxes are:

- `[ ]` — incomplete
- `[x]` — complete

Every checkbox in an iteration is required for completion. Do not use checkboxes for deferred work or custom checkbox states.

Iteration files require only:

- title
- status
- sources when applicable
- task checkboxes

Add sections such as dependencies, deferred work, open questions, or completion notes only when useful. Record deferred work as plain list items, not checkboxes.

Multi-agent iterations may additionally give tasks stable IDs and include an `Execution plan`. Each task belongs to exactly one execution group, while one execution group may contain one task, several tasks, or the whole iteration. Execution groups have stable IDs, may depend on other groups, and name one Worker profile available in runtime configuration. Group completion is inferred from its task checkboxes; do not introduce a separate execution-group checkbox or custom task state.

Runtime configuration owns all CLI, provider, model, and Worker-profile mappings. PMD does not assume which runtime is stronger or cheaper. Planner selects a configured Worker profile based primarily on expected difficulty and capability requirements. Coordinator must use that profile and must not silently substitute another unless runtime configuration explicitly defines a fallback.

### Changelog

User-visible completed outcomes live in `docs/changelog.md`.

Create or update the changelog only once, after the user approves completion of an iteration following the latest readiness report. The request that starts a completion review is not archive approval, even if it asks to approve or archive.

Every changelog entry must reference the archived iteration that delivered it. Do not add planned, speculative, or unfinished work.

## Multi-agent roles and decisions

Multi-agent PMD separates four logical roles:

- **Coordinator** owns lifecycle state, routing, dependency progress, user interaction, manual validation, and task checkbox updates. It does not design or implement the technical solution.
- **Planner** owns technical planning, task decomposition, execution groups, dependencies, acceptance criteria, manual-validation design, and Worker-profile assignment. It never implements code.
- **Worker** implements and directly validates only its assigned execution group. It does not perform the PMD simplification review and never updates iteration task state.
- **Reviewer** independently reviews correctness, tests, scope, maintainability, and simplification. It reports findings and does not implement fixes by default.

All roles read `docs/agent-policy.md` when it exists. That optional file may reserve additional decisions for the user and assign ordinary technical decisions to Planner or Worker. Regardless of policy, creating, changing, renaming, or deleting a spec always requires explicit user approval, and completing or archiving an iteration always requires fresh explicit approval through `pmd-complete`.

Worker and Reviewer return blockers and required decisions to Coordinator. Coordinator routes implementation-planning issues to Planner and protected decisions to the user. Only Coordinator normally interacts with the user during coordinated implementation.

For required manual validation, Planner defines the expected behaviour, Worker may supply setup notes, Reviewer confirms readiness, and Coordinator presents one actionable step at a time. Coordinator checks tasks only after Worker validation, Reviewer `PASS`, and all required manual validation pass. A failed manual test starts another Worker → Reviewer → manual-validation cycle.

## Completion rules

A task is complete only when:

- implementation is finished
- relevant automated tests, linters, type checks, build checks, agent-performed validation, and other checks pass at the workflow's required gates
- the implementation and directly related code have passed the workflow's simplification review, with safe simplifications applied
- any meaningful simplification tradeoff has been decided by the user
- any required user-run manual validation passes
- acceptance criteria are satisfied
- affected documentation is consistent

In the single-agent workflow, `pmd-implement` implements and validates the work, performs simplification, reruns relevant checks even when simplification produces no changes, conducts any manual validation with the user, and updates task state.

In the multi-agent workflow, Worker implements and directly validates without a separate simplification pass. Reviewer performs the mandatory correctness, validation, scope, maintainability, and simplification review. Coordinator then conducts any manual validation and updates task state. Reviewer must explicitly state when no meaningful simplification exists.

Keep simplification related to the selected implementation rather than expanding it into unrelated cleanup. Do not silently choose a simplification that trades off behaviour, scope, compatibility, performance, maintainability, or risk; return the decision through the workflow and wait for the user's choice.

Whenever user review or manual validation leads to implementation changes, repeat all gates required by the selected workflow before returning the revised work for further validation or acceptance.

Do not update a spec merely because implementation was completed. A spec should change only when expected behaviour must be clarified or changed, and only after explicit spec-change approval from the user. If user suggests something different from the spec at some point, ask them if you should change the spec to match that.

Iteration-completion approval is a separate gate handled by `pmd-complete`. An iteration must not be marked `Completed`, added to the changelog, or archived without that approval.

A prior multi-agent Reviewer result does not replace `pmd-complete`'s fresh readiness review. Both single-agent and multi-agent iterations use the same completion and archive path.

An iteration must not be archived with any unchecked checkbox. A task intentionally removed from scope must be moved to a clearly labeled deferred section as a plain list item or to `docs/inbox.md`.

Before making any completion changes, verify that the iteration has status `Awaiting approval`, still exists only in `docs/tasks/current/`, and has no changelog entry or archived-path reference. If any check fails, stop without changing files; never overwrite an archived iteration or duplicate its changelog entry.

## Source-of-truth order

When documents conflict, use this priority:

1. Explicit current user instruction
2. PRD
3. Spec
4. Current iteration file
5. Existing implementation
6. Inbox entry

Do not silently resolve meaningful contradictions. Explain them and update the appropriate source.

## Workflow skills

Use the relevant project skill for procedural work:

- `pmd-setup` — initialize the PMD folder and file structure
- `pmd-update` — update an existing PMD installation while preserving project documentation and non-PMD instructions
- `pmd-plan` — discuss the scope, draft any needed specs, and create a single-agent plan or optional multi-agent execution plan
- `pmd-implement` — use one agent to implement, simplify, validate, and update task state for all or part of an iteration
- `pmd-worker` — implement and directly validate one Coordinator-assigned execution group without simplifying or updating PMD state
- `pmd-review` — independently review a multi-agent execution group or whole iteration, including mandatory simplification review
- `pmd-coordinate` — manage serial multi-agent execution, review loops, manual validation, and task state
- `pmd-complete` — verify, request approval, update changelog, and archive an iteration

After finishing a skill, suggest the appropriate next workflow step:

- setup → restart the agent or start a new session, briefly describe the app requirements in `docs/prd/`, then use `pmd-plan`
- update → restart the agent or start a new session so it reloads the updated PMD skills and instructions
- single-agent planning → `pmd-implement`
- multi-agent planning → `pmd-coordinate`
- implementation with behaviour not covered by or inconsistent with current specs → resolve the gap before continuing by fixing the implementation, or by discussing and drafting the needed spec addition or update and obtaining explicit spec-change approval
- partial implementation → `pmd-implement` for another part
- full implementation → `pmd-complete`
- coordinated implementation → continue `pmd-coordinate` until its whole-iteration review passes, then use `pmd-complete`
- completion → `pmd-implement` for another current iteration, or `pmd-plan` when new planning is needed

<!-- PMD:END -->
