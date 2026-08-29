# Plain Markdown Development

A lightweight, agent-assisted workflow for structuring features, planning what comes next, and developing iteratively, with Markdown and Git as durable state. Use it with one coding agent or add optional multi-agent coordination for larger work.

## Flow

```text
Single-agent:
Requirements → pmd-plan → pmd-implement → pmd-complete

Multi-agent:
Requirements → Planner / pmd-plan → pmd-coordinate → pmd-complete
```

Loose ideas, bugs, and questions go to `docs/inbox.md`.

## Installation

Copy the complete `.agents/skills/` directory into your repository, then ask your coding agent:

```text
Use pmd-setup to install PMD in this repository.
```

After setup, restart the agent or start a new session so it reloads the applicable instruction file.

## Quick Start

Add lightweight product requirements to `docs/prd/`. They do not need to be large or complete PRDs—simple high-level notes about users, desired behaviour, and constraints are enough. You can think of them as **product briefs**.

Then use the simple workflow:

```text
pmd-plan       → discuss scope, draft needed specs, create iteration
pmd-implement  → implement, verify, and simplify all or selected iteration tasks
pmd-complete   → verify, request approval, archive and update changelog
```

You can just type the skill name or add some context like here:

```text
Use pmd-plan to plan dark mode implementation
```

This remains the default workflow and needs no runtime configuration. `pmd-implement` uses one agent to implement, run checks, simplify, conduct any manual validation, and update task state.

## Optional multi-agent workflow

Multi-agent PMD is useful when an iteration benefits from stronger separation between technical planning, implementation, and independent review, or when different execution groups need different configured coding capabilities.

It separates four logical roles:

- **Coordinator** owns process state, routing, user interaction, manual validation, and task checkbox updates.
- **Planner** owns technical planning, dependencies, execution grouping, acceptance criteria, and Worker-profile assignment.
- **Worker** implements and directly validates an assigned execution group without changing PMD task state.
- **Reviewer** independently checks correctness, tests, scope, maintainability, and simplification without implementing fixes by default.

Planner adds stable task IDs and an optional `Execution plan` to the iteration. Small PMD tasks remain independently verifiable, but one execution group—and therefore one Worker invocation—may cover one task, several related tasks, or the entire iteration. Groups may depend on other groups.

Every execution group names a Worker profile from runtime configuration. A profile maps a user-chosen label such as `default`, `strong`, or `frontend` to a CLI, provider, model, or native subagent. PMD does not prescribe those mappings. Planner selects the profile from expected difficulty and capability needs; Coordinator must not silently replace that choice.

The coordinated loop is:

```text
Worker implementation + direct validation
    → Reviewer correctness + simplification review
    → Coordinator-led manual validation when required
    → task state update
    → next execution group
```

After a fresh whole-iteration review, Coordinator sets `Awaiting approval` and directs the user to the same `pmd-complete` approval and archive workflow used by single-agent PMD.

Core PMD requires no custom orchestration application, database, daemon, message queue, background Worker, or worktree. Runtime integration is optional and provider/model choices remain configurable. See the [OpenCode v2 reference integration](integrations/opencode/README.md) for a serial setup with one OpenCode-native Worker profile, one external Codex CLI profile, read-only Reviewer permissions, and Git checkpoint commits.

Projects may optionally copy the bundled [agent policy template](.agents/skills/pmd-coordinate/references/agent-policy.md) to `docs/agent-policy.md` and adapt additional protected decision boundaries. Setup does not create this file or runtime configuration automatically.

## Updating

For an existing installation, ask your coding agent:

```text
Use pmd-update to update PMD in this repository.
```

## Structure

```text
docs/
├── prd/              # lightweight product requirements
├── specs/            # detailed behaviour for work being prepared
├── tasks/
│   ├── current/      # planned or in-progress iterations
│   └── archived/     # approved, completed iterations
├── inbox.md          # loose ideas, bugs, and questions
└── changelog.md      # delivered user-visible changes
```

During planning, the agent usually drafts any needed specs from the product requirements, repository context, and discussion with you. You approve every spec before it is saved.

Small bugs, maintenance work, and technical tasks can go directly into an iteration. Multiple current iterations are allowed when useful.

Every iteration checkbox is required for completion. If a task is no longer needed, move it to a clearly labeled deferred section as a plain list item or return it to `docs/inbox.md`.
