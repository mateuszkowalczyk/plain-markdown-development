# Repository instructions

## Scope

- This repository is the source distribution for Plain Markdown Development, not a PMD-initialized application. Do not create the consumer `docs/` structure here unless explicitly asked.
- Product behavior is implemented as Markdown skills under `.agents/skills/`; there is no application code, package manifest, CI, or automated test suite.

## Sources of truth

- `.agents/skills/pmd-setup/references/agents-instructions.md` is the complete marked block installed into a consumer repository's selected `AGENTS.md` or `CLAUDE.md`. Keep both `<!-- PMD:START -->` and `<!-- PMD:END -->`; setup replaces exactly that range and preserves everything outside it.
- Each `.agents/skills/*/SKILL.md` owns its procedure and trigger description. Keep the frontmatter `name` identical to its lowercase, hyphen-separated skill directory name.
- `.agents/skills/pmd-setup/assets/opencode/` is the source of truth for installable OpenCode configuration.
- `README.md` is the user-facing installation and quick-start overview. Update it when installation steps, skill names, or the documented workflow change.

## Workflow invariants

- One PMD task is sized for one Builder context, has one Builder tier, receives one independent review, and ends with one approval and one final commit. PMD has no nested task, checkbox, execution-group, or standalone Planner concepts.
- New work uses `docs/tasks/current/task-NNN-short-name.md`; completion moves the same filename to `docs/tasks/archived/`. New numbering is higher than every historical `task-NNN-*` or `iteration-NNN-*` file. Archived iteration files remain untouched.
- Every task uses the single schema defined by PMD instructions. Builder may update only its `Plan` and `Validation` sections and must not stage files or create commits; Coordinator owns scope, tier, status, review state, manual-validation state, and lifecycle. Reviewer is read-only.
- Any spec file change requires explicit user approval.
- Coordinator may set `Awaiting approval` after implementation, review, and any manual-validation gates pass, but must not update the changelog, archive a task, or mark it `Completed` outside approved `pmd-complete` Stage 2.
- Completion is two-stage: Stage 1 checks integrity and readiness without repeating implementation review, including that all reviewed contract fields and the complete reviewed diff remain unchanged except Coordinator gate records in Review, Manual validation, and Status. Only explicit approval permits the changelog update, `Completed` status, archival, staging, and the task's one final commit; `pmd-complete` Stage 2 creates that commit as its last repository mutation.
- Setup must be idempotent: preserve existing files and all selected instruction-file content outside the PMD markers, support installation in `AGENTS.md`, `CLAUDE.md`, or both, leave any unselected instruction file unchanged, report a complete PMD block there as potentially stale or intentionally shared, never duplicate the block, and ask before handling a single unmatched marker or overlapping documentation layout.

## Coordination invariants

- PMD has one coordinated workflow with three logical roles: Coordinator owns the ongoing project workflow and durable task state, Builder plans, implements, and directly validates one task, and Reviewer independently reviews it.
- Every PMD project must have `docs/agent-policy.md` and usable runtime configuration. Setup creates the policy from the bundled default after summarizing the decision boundaries and asking the user for changes, then configures the runtime.
- Setup configures separate `simple` and `complex` Builder profiles. When the runtime supports per-role models, setup proposes cost-aware model strengths for Coordinator, both Builders, and the single Reviewer, then records the user's exact choices without inventing identifiers.
- CLI, provider, model, and Builder-profile mappings belong to runtime configuration. Core PMD must not prescribe a concrete runtime or silently substitute one configured profile for another. Coordinator assigns the one tier used for the whole task.
- Builder plans, implements, and directly validates the whole task in one context. It never changes task scope, tier, status, review state, or lifecycle state and does not perform the separate PMD simplification review.
- PMD uses one Reviewer role. Reviewer owns correctness, scope, validation, maintainability, and the mandatory simplification review, requires only materially beneficial corrections, omits non-actionable nits, and never implements fixes.
- Every task receives exactly one review boundary covering its complete diff. Corrections return to the same Builder context and tier when possible, followed by fresh direct validation and review.
- Coordinator is the normal interface to the user throughout the coordinated project loop. Builders and Reviewers return protected decisions, blockers, and manual-validation information to Coordinator instead of contacting the user directly. Coordinator advances through task creation, execution, review, readiness review, completion, and the next task without requiring the user to name skills or approve procedural handoffs. It pauses only for approvals, protected decisions, ambiguous choices, or user-run validation, using an available question tool or a concise short-answer prompt.
- Markdown and Git remain the durable workflow state. PMD must not require a database, message queue, daemon, background worker system, or other custom orchestration service.

## Verification

- No repository commands are defined. Review Markdown changes directly and cross-check shared lifecycle rules across the affected skill, the installed instruction template, and `README.md` where user-facing behavior changes.
