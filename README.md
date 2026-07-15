# Plain Markdown Development

A lightweight workflow for planning and delivering product work with Markdown and agent skills.

## Flow

```text
Requirements → Specs → Iterations → Implementation → Approval → Archive + Changelog
```

Loose ideas, bugs, and questions go to `docs/inbox.md`.

## Installation

Copy `.agents/skills/` into your repository, then ask your coding agent:

```text
Use pmd-setup to install PMD in this repository.
```

After setup restart the agent or start a new session so it reloads the selected instruction file.

## Quick Start

Add lightweight product requirements to `docs/prd/`. They do not need to be large or complete PRDs—simple high-level notes about users, desired behaviour, and constraints are enough. You can think of them as **product briefs**.

Then use the skills in the following order:

```text
pmd-plan       → discuss scope, draft needed specs, create iteration
pmd-implement  → implement all or selected iteration tasks
pmd-complete   → verify, request approval, archive and update changelog
```

You can just type the skill name or add some context like here:

```text
Use pmd-plan to plan dark mode implementation
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
