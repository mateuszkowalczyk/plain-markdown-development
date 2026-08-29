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

PMD uses one coordinated workflow:

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

Iteration files require:

- title
- status
- sources when applicable
- task checkboxes with stable IDs
- an execution plan

Add sections such as dependencies, deferred work, open questions, or completion notes only when useful. Record deferred work as plain list items, not checkboxes.

Each task belongs to exactly one execution group, while one execution group may contain one task, several tasks, or the whole iteration. Execution groups have stable IDs, may depend on other groups, and name one Worker profile available in runtime configuration. Group completion is inferred from its task checkboxes; do not introduce a separate execution-group checkbox or custom task state.

Runtime configuration owns all CLI, provider, model, and Worker-profile mappings. PMD does not assume which runtime is stronger or cheaper. Planner selects a configured Worker profile based primarily on expected difficulty and capability requirements. Coordinator must use that profile and must not silently substitute another unless runtime configuration explicitly defines a fallback.

### Changelog

User-visible completed outcomes live in `docs/changelog.md`.

Create or update the changelog only once, after the user approves completion of an iteration following the latest readiness report. The request that starts a completion review is not archive approval, even if it asks to approve or archive.

Every changelog entry must reference the archived iteration that delivered it. Do not add planned, speculative, or unfinished work.

## Roles and decisions

PMD separates four logical roles:

- **Coordinator** owns the ongoing coordinated project workflow across iterations: lifecycle state, routing, dependency progress, user interaction, manual validation, task checkbox updates, and handoffs to completion and next-iteration planning. It does not design or implement the technical solution.
- **Planner** owns technical planning, task decomposition, execution groups, dependencies, acceptance criteria, manual-validation design, and Worker-profile assignment. It never implements code.
- **Worker** implements and directly validates only its assigned execution group. It does not perform the PMD simplification review and never updates iteration task state.
- **Reviewer** independently reviews correctness, tests, scope, maintainability, and simplification. It reports findings and does not implement fixes by default.

Every PMD project must have `docs/agent-policy.md`. The policy reserves project-specific decisions for the user and assigns ordinary technical decisions to Planner or Worker. Setup creates it from the bundled default after summarizing the boundaries and asking the user for changes. Regardless of policy contents, creating, changing, renaming, or deleting a spec always requires explicit user approval, and completing or archiving an iteration always requires fresh explicit approval through `pmd-complete`.

Worker and Reviewer return blockers and required decisions to Coordinator. Coordinator routes implementation-planning issues to Planner and protected decisions to the user. Only Coordinator normally interacts with the user throughout the coordinated project loop.

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

Worker implements and directly validates without a separate simplification pass. Reviewer performs the mandatory correctness, validation, scope, maintainability, and simplification review. Coordinator then conducts any manual validation and updates task state. Reviewer must explicitly state when no meaningful simplification exists.

After the whole-iteration review passes, Coordinator automatically follows `pmd-complete` Stage 1. That procedural handoff is not archive approval: `pmd-complete` performs a fresh readiness review and asks concisely for explicit approval before changing the changelog or archive. After approved completion, it stages only iteration-related work and creates the iteration's final commit as the last repository mutation. Coordinator then automatically continues with another clearly prioritized planned iteration or uses `pmd-plan` for clear remaining work.

Coordinator minimizes user effort. It invokes applicable skills and advances between procedural stages without asking when no approval or choice is required. When a spec change, archive approval, protected decision, ambiguous priority, or user-run validation requires input, Coordinator uses an available question tool when practical or asks for a short answer such as yes/no. It asks for the decision itself, not for the user to type a skill name. General consent to continue never substitutes for a required approval.

Keep simplification related to the selected implementation rather than expanding it into unrelated cleanup. Do not silently choose a simplification that trades off behaviour, scope, compatibility, performance, maintainability, or risk; return the decision through the workflow and wait for the user's choice.

Whenever user review or manual validation leads to implementation changes, repeat all gates required by the selected workflow before returning the revised work for further validation or acceptance.

Do not update a spec merely because implementation was completed. A spec should change only when expected behaviour must be clarified or changed, and only after explicit spec-change approval from the user. If user suggests something different from the spec at some point, ask them if you should change the spec to match that.

Iteration-completion approval is a separate gate handled by `pmd-complete`. An iteration must not be marked `Completed`, added to the changelog, or archived without that approval.

A prior Reviewer result does not replace `pmd-complete`'s fresh readiness review.

An iteration must not be archived with any unchecked checkbox. A task intentionally removed from scope must be moved to a clearly labeled deferred section as a plain list item or to `docs/inbox.md`.

Before making any completion changes, verify that the iteration has status `Awaiting approval`, still exists only in `docs/tasks/current/`, and has no changelog entry or archived-path reference. If any check fails, stop without changing files; never overwrite an archived iteration or duplicate its changelog entry.

After approved completion changes, stage only work belonging to that iteration and create its final commit as the last repository mutation of `pmd-complete`. Do not include unrelated staged or working-tree changes. If the commit fails, report the uncommitted completion state and do not continue to another iteration.

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

- `pmd-setup` — initialize PMD and configure its required policy, runtime, and provider files
- `pmd-update` — update an existing PMD installation while preserving project documentation and non-PMD instructions
- `pmd-coordinate` — the user-facing entrypoint for planning, implementation, review, completion, and subsequent iterations
- `pmd-plan` — when delegated by Coordinator, discuss the scope, draft any needed specs, and create a coordinated execution plan
- `pmd-worker` — implement and directly validate one Coordinator-assigned execution group without simplifying or updating PMD state
- `pmd-review` — independently review an execution group or whole iteration, including mandatory simplification review
- `pmd-complete` — when invoked by Coordinator, verify, request approval, update changelog, archive an iteration, and commit its completed work

After finishing a skill, suggest the appropriate next workflow step:

- setup → restart the agent or start a new session, briefly describe the app requirements in `docs/prd/`, then use `pmd-coordinate`
- update → restart the agent or start a new session so it reloads the updated PMD skills and instructions
- planning → return to `pmd-coordinate`
- implementation with behaviour not covered by or inconsistent with current specs → resolve the gap before continuing by fixing the implementation, or by discussing and drafting the needed spec addition or update and obtaining explicit spec-change approval
- Worker or Reviewer result → return to `pmd-coordinate`
- coordinated implementation → continue `pmd-coordinate`; it automatically runs the completion readiness review, requests required archive approval, commits approved completion, and continues to the next clear iteration or planning step
- completion → return to `pmd-coordinate`, which continues with the next clear current iteration or `pmd-plan`

<!-- PMD:END -->
