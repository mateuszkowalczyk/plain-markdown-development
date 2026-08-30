# Plain Markdown Development

A lightweight, coordinated workflow for structuring features, planning what comes next, and developing iteratively, with Markdown and Git as durable state.

Loose ideas, bugs, and questions go to `docs/inbox.md`.

## Installation

Copy the complete `.agents/skills/` directory into your repository, then ask your coding agent:

```text
Use pmd-setup to install PMD in this repository.
```

After setup, restart the agent or start a new session so it reloads the applicable instruction file.

Setup summarizes the required default agent policy, asks for project-specific changes, creates `docs/agent-policy.md`, and configures a runtime. If the project uses OpenCode, setup asks whether to install the bundled reference runtime.

## Quick Start

Add lightweight product requirements to `docs/prd/`. They do not need to be large or complete PRDs—simple high-level notes about users, desired behaviour, and constraints are enough. You can think of them as **product briefs**.

Then ask the agent to coordinate the project:

```text
Use pmd-coordinate to implement the requirements.
```

Coordinator is the single user-facing workflow entrypoint. It leads the user through planning, implementation, review, required approvals, completion, and subsequent iterations; the other workflow skills are invoked internally.

## How coordination works

It separates four logical roles:

- **Coordinator** guides the project across iterations, owns process state and routing, conducts user validation, updates task state, and invokes the next workflow stage whenever no user decision is required.
- **Planner** owns technical planning, dependencies, execution grouping, acceptance criteria, and Worker-profile assignment.
- **Worker** implements and directly validates an assigned execution group without changing PMD task state.
- **Reviewer** independently checks correctness, tests, scope, maintainability, and looks for simplifications without implementing fixes by default.

Planner adds stable task IDs and an `Execution plan` to the iteration. Small PMD tasks remain independently verifiable, but one execution group—and therefore one Worker invocation—may cover one task, several related tasks, or the entire iteration. Groups may depend on other groups.

Every execution group names a Worker profile from runtime configuration. A profile maps a user-chosen label such as `default`, `strong`, or `frontend` to a CLI, provider, model, or native subagent. PMD does not prescribe those mappings. Planner selects the profile from expected difficulty and capability needs; Coordinator must not silently replace that choice.

After a fresh whole-iteration review, Coordinator automatically starts the `pmd-complete` readiness workflow. It asks for a short explicit approval only when the iteration is ready to archive. Once approved, `pmd-complete` updates the changelog, archives the iteration, and creates its final commit. Coordinator then continues with the next clearly selected iteration or invokes `pmd-plan` for clear remaining work. When a real decision is required, it asks concisely—preferably with an available question tool—without requiring the user to know or type skill names.

Core PMD requires no custom orchestration application, database, daemon, message queue, background Worker, or worktree. Runtime and provider/model choices remain configurable. See the [OpenCode v2 reference integration](integrations/opencode/README.md) for a serial setup with one OpenCode-native Worker profile, read-only Reviewer permissions, and Git checkpoint commits. Its [historical MVP dogfood report](integrations/opencode/dogfood-report.md) records an end-to-end exercise that also tested a custom external Worker profile.

Every PMD project requires `docs/agent-policy.md`. During setup, PMD summarizes the bundled [default policy](.agents/skills/pmd-coordinate/references/agent-policy.md), asks which boundaries should change, and creates the project policy.

## Updating

For an existing installation, ask your coding agent:

```text
Use pmd-update to update PMD in this repository.
```

## Structure

```text
docs/
├── agent-policy.md  # project-specific agent decision boundaries
├── prd/             # lightweight product requirements
├── specs/           # detailed behaviour for work being prepared
├── tasks/
│   ├── current/      # planned or in-progress iterations
│   └── archived/     # approved, completed iterations
├── inbox.md          # loose ideas, bugs, and questions
└── changelog.md      # delivered user-visible changes
```

During planning, the agent usually drafts any needed specs from the product requirements, repository context, and discussion with you. You approve every spec before it is saved.

Small bugs, maintenance work, and technical tasks can go directly into an iteration. Multiple current iterations are allowed when useful.

Every iteration checkbox is required for completion. If a task is no longer needed, move it to a clearly labeled deferred section as a plain list item or return it to `docs/inbox.md`.
