# Repository instructions

## Scope

- This repository is the source distribution for Plain Markdown Development, not a PMD-initialized application. Do not create the consumer `docs/` structure here unless explicitly asked.
- Product behavior is implemented as Markdown skills under `.agents/skills/`; there is no application code, package manifest, CI, or automated test suite.

## Sources of truth

- `.agents/skills/pmd-setup/references/agents-instructions.md` is the complete marked block installed into a consumer repository's selected `AGENTS.md` or `CLAUDE.md`. Keep both `<!-- PMD:START -->` and `<!-- PMD:END -->`; setup replaces exactly that range and preserves everything outside it.
- Each `.agents/skills/*/SKILL.md` owns its procedure and trigger description. Keep the frontmatter `name` identical to its lowercase, hyphen-separated skill directory name.
- `README.md` is the user-facing installation and quick-start overview. Update it when installation steps, skill names, or the documented workflow change.

## Workflow invariants

- Planning may create an iteration but must not implement it; any spec file change requires explicit user approval.
- Implementation may check tasks and set `Awaiting approval`, but must not update the changelog, archive an iteration, or mark it `Completed`.
- Completion is two-stage: readiness review is not archive approval. Only explicit approval permits the changelog update, `Completed` status, and move from `docs/tasks/current/` to `docs/tasks/archived/` without renaming.
- Setup must be idempotent: preserve existing files and all selected instruction-file content outside the PMD markers, support installation in `AGENTS.md`, `CLAUDE.md`, or both, leave any unselected instruction file unchanged, report a complete PMD block there as potentially stale or intentionally shared, never duplicate the block, and ask before handling a single unmatched marker or overlapping documentation layout.

## Multi-agent invariants

- Multi-agent PMD is optional. The single-agent `pmd-plan` → `pmd-implement` → `pmd-complete` workflow remains supported without runtime configuration or additional roles.
- Multi-agent PMD separates four logical roles: Coordinator owns lifecycle and process state, Planner owns technical planning, Worker implements an assigned execution group, and Reviewer independently reviews it.
- CLI, provider, model, and Worker-profile mappings belong to runtime configuration. Core PMD must not prescribe a concrete runtime or silently substitute one configured profile for another.
- In multi-agent mode, Workers perform implementation and direct validation but never update iteration checkboxes or status. Coordinator updates task state only after implementation, review, and any required manual validation succeed.
- Reviewer owns correctness, scope, validation, maintainability, and the mandatory multi-agent simplification review. Worker must not duplicate that simplification pass.
- Coordinator is the normal interface to the user during execution. Workers and Reviewers return protected decisions, blockers, and manual-validation information to Coordinator instead of contacting the user directly.
- Markdown and Git remain the durable workflow state. Multi-agent PMD must not require a database, message queue, daemon, background worker system, or other custom orchestration service.

## Verification

- No repository commands are defined. Review Markdown changes directly and cross-check shared lifecycle rules across the affected skill, the installed instruction template, and `README.md` where user-facing behavior changes.
