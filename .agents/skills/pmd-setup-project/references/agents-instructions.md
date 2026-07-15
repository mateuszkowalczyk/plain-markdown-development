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

Development follows this progression:

`PRD → Spec → Iteration tasks → Implementation → Archived iteration`

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

Every spec has one status:

- `Draft` — incomplete or blocked by unresolved questions.
- `Ready` — clear enough to create implementation tasks.

Specs are required for meaningful product behaviour, but not for every small bug, research task, refactor, or maintenance item.

The agent should discuss unclear behaviour with the user and propose the resulting spec. Always obtain explicit user approval before creating, editing, renaming, deleting, or changing the status of a spec.

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

Do not use custom checkbox states.

Iteration files require only:

- title
- status
- sources when applicable
- task checkboxes

Add sections such as dependencies, deferred work, open questions, or completion notes only when useful.

### Changelog

User-visible completed outcomes live in `docs/changelog.md`.

Create or update the changelog only once, after the user approves completion of an iteration.

Every changelog entry must reference the archived iteration that delivered it. Do not add planned, speculative, or unfinished work.

## Completion rules

A task is complete only when:

- implementation is finished
- relevant tests and checks pass
- acceptance criteria are satisfied
- affected documentation is consistent

Do not update a spec merely because implementation was completed. A spec should change only when expected behaviour must be clarified or changed, and only after explicit user approval.

An iteration must not be archived without explicit user approval.

An iteration must not be archived with unchecked required tasks. Work intentionally removed from scope must be moved to another documented destination and explained when relevant.

## Source-of-truth order

When documents conflict, use this priority:

1. Explicit current user instruction
2. PRD
3. `Ready` spec
4. Current iteration file
5. Existing implementation
6. Inbox entry

Do not silently resolve meaningful contradictions. Explain them and update the appropriate source.

## Workflow skills

Use the relevant project skill for procedural work:

- `pmd-setup-project` — initialize the PMD folder and file structure
- `pmd-plan-iteration` — discuss the scope, draft any needed specs, and plan an iteration
- `pmd-implement-iteration` — implement all or part of an iteration
- `pmd-complete-iteration` — verify, request approval, update changelog, and archive an iteration

<!-- PMD:END -->
