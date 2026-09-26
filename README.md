# Plain Markdown Development

A lightweight, multi-agent workflow that balances human control over product decisions with agent autonomy in coordination, implementation, and review. A Coordinator guides each task, a tiered Builder implements it, and an independent Reviewer checks the result.

## Installation

Copy the complete `.agents/skills/` directory into your repository, then send:

```text
pmd-setup
```

After setup, restart the agent or start a new session so it reloads the applicable instruction file.

Setup:

- creates the PMD documentation workspace
- installs the PMD instruction block in root `AGENTS.md` while preserving existing content
- configures the agent policy, runtime, and role-specific models

OpenCode V2 users can install the bundled reference runtime, which defines the required PMD agents. Setup uses models available in your project and asks you to confirm the model for each role. Start a new OpenCode session after setup to verify that `pmd-coordinator` and its configured model are selected.

## Quick start

Add lightweight product requirements to `docs/prd/`. They can be brief notes about users, desired behaviour, and constraints rather than large formal documents. Then send:

```text
pmd-coordinate
```

Coordinator takes it from there and invokes the other workflow skills internally. Your runtime may separately request permission for commands outside its routine allowlist.

## How coordination works

```text
You
 │
 ▼
Coordinator ──→ Builder (simple or complex) ──→ Reviewer ──→ Approval ──→ Archive
    ▲                                                                      │
    └──────────────────────────── next task ───────────────────────────────┘
```

One PMD task is deliberately small enough for one Builder context. It has one coherent outcome, one Builder tier, one complete-diff review, one completion approval, and one final commit.

- **Coordinator** creates and prioritizes tasks, chooses a Builder tier, owns durable workflow state, leads user validation, and handles approval and completion.
- **Builder** plans, implements, tests, and directly validates the whole task in one context. It never stages files or creates commits.
- **Reviewer** independently checks correctness, validation, scope, maintainability, and possible simplifications. It can run additional checks and use temporary scratch scripts, but does not change repository or Git state; commands outside its routine inspection allowlist may request runtime permission.

## Quality and approval

Every task is independently reviewed and, when needed, validated by the user. The Coordinator asks for explicit approval before updating the changelog, archiving the task, and creating its final commit.

Spec changes also require explicit approval. Project-specific decision boundaries live in `docs/agent-policy.md`.

## Updating

For an existing installation, send:

```text
pmd-update
```

The updater replaces PMD-owned skills and the PMD block in root `AGENTS.md` while preserving project documentation and detected local customizations. It can also synchronize an accepted bundled OpenCode runtime from the former Planner/Worker agent names to Builder agents.

## Structure

```text
docs/
├── agent-policy.md  # project decision boundaries
├── prd/             # product intent: users, problems, goals, constraints
├── specs/           # precise expected behaviour of individual capabilities
├── tasks/
│   ├── current/     # open or awaiting-approval tasks
│   └── archived/    # approved, completed tasks
├── inbox.md         # loose ideas, bugs, and questions
└── changelog.md     # delivered user-visible outcomes
```

Small bugs, maintenance work, and technical tasks can become tasks directly without dedicated PRDs or specs when they do not change intended product behaviour.
