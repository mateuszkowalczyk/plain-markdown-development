# Plain Markdown Development

A lightweight, multi-agent workflow for planning, implementing, and reviewing software with Markdown and Git as durable project state.

## Installation

Copy the complete `.agents/skills/` directory into your repository, then send:

```text
pmd-setup
```

After setup, restart the agent or start a new session so it reloads the applicable instruction file.

Setup:

- creates the PMD documentation workspace
- installs repository instructions while preserving existing content
- configures the agent roles and runtime

OpenCode users can install the bundled reference runtime during setup.

## Quick Start

Add lightweight product requirements to `docs/prd/`. They do not need to be large or complete PRDs—simple high-level notes about users, desired behaviour, and constraints are enough. You can think of them as **product briefs**.

Then send:

```text
pmd-coordinate
```

Coordinator takes it from there, invoking the other workflow skills internally.

## How coordination works

```text
You
 │
 ▼
Coordinator
 │
 └─ Planner → Worker → Reviewer → Approval → Archive
      ▲                                      │
      └──────────── next iteration ──────────┘
```

**Coordinator** is the single user-facing role. **Planner** turns selected requirements into an execution plan, **Worker** implements it, and **Reviewer** independently checks the result and looks for simplifications.

Key properties:

- **Coordinator** advances automatically and pauses only for required decisions, manual validation, or explicit approval.
- The project **agent policy** defines who may make which decisions.
- **Markdown and Git** provide durable state without a custom orchestration service.
- **Runtime configuration** maps each role to an agent or CLI; the **provider and model** behind it remain configurable. See the [OpenCode v2 reference integration](integrations/opencode/README.md) for a concrete setup.

## Updating

For an existing installation, send:

```text
pmd-update
```

## Structure

```text
docs/
├── agent-policy.md  # who may make which project decisions
├── prd/             # product intent: users, problems, goals, constraints
├── specs/           # precise expected behaviour of individual capabilities
├── tasks/           # executable iteration plans and delivery progress
│   ├── current/      # planned or in-progress iterations
│   └── archived/     # approved, completed iterations
├── inbox.md          # loose ideas, bugs, and questions
└── changelog.md      # delivered user-visible changes
```

A PRD is a lightweight product brief: it explains what should be achieved and why. A spec narrows one capability into exact expected behaviour. An iteration selects concrete work from those sources and tracks it through delivery.

During planning, the agent drafts needed specs from PRDs, repository context, and discussion with you. You approve every spec before it is saved.

Small bugs, maintenance work, and technical tasks can go directly into an iteration. Multiple current iterations are allowed when useful.

Every iteration checkbox is required for completion. If a task is no longer needed, move it to a clearly labeled deferred section as a plain list item or return it to `docs/inbox.md`.
