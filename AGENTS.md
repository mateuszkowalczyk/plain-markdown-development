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

- Planning may create an iteration but must not implement it; any spec file change requires explicit user approval.
- Coordinator may check tasks and set `Awaiting approval` after the required implementation and review gates, but must not update the changelog, archive an iteration, or mark it `Completed` outside approved `pmd-complete` Stage 2.
- Completion is two-stage: readiness review is not archive approval. Only explicit approval permits the changelog update, `Completed` status, and move from `docs/tasks/current/` to `docs/tasks/archived/` without renaming. After those changes, `pmd-complete` creates the iteration's final commit as its last repository mutation.
- Setup must be idempotent: preserve existing files and all selected instruction-file content outside the PMD markers, support installation in `AGENTS.md`, `CLAUDE.md`, or both, leave any unselected instruction file unchanged, report a complete PMD block there as potentially stale or intentionally shared, never duplicate the block, and ask before handling a single unmatched marker or overlapping documentation layout.

## Coordination invariants

- PMD has one coordinated workflow with four logical roles: Coordinator owns the ongoing project workflow and process state across iterations, Planner owns technical planning, Worker implements an assigned execution group, and Reviewer independently reviews it.
- Every PMD project must have `docs/agent-policy.md` and usable runtime configuration. Setup creates the policy from the bundled default after summarizing the decision boundaries and asking the user for changes, then configures the runtime.
- Setup configures separate `simple` and `complex` Worker profiles. When the runtime supports per-role models, setup proposes cost-aware model strengths for Coordinator, Planner, both Workers, and the single Reviewer, then records the user's exact choices without inventing identifiers.
- CLI, provider, model, and Worker-profile mappings belong to runtime configuration. Core PMD must not prescribe a concrete runtime or silently substitute one configured profile for another. Planner classifies each execution group as `simple` or `complex` and assigns a profile configured for that tier.
- Planner uses the fewest coherent execution groups that preserve context, dependencies, Worker difficulty, and useful review or manual-validation boundaries; task granularity must not dictate agent-invocation granularity.
- Workers perform implementation and direct validation but never update iteration checkboxes or status. Coordinator updates task state only after implementation, review, and any required manual validation succeed.
- PMD uses one Reviewer role. Reviewer owns correctness, scope, validation, maintainability, and the mandatory simplification review, requires only materially beneficial corrections, and omits non-actionable nits. Worker must not duplicate that simplification pass.
- Every execution group receives review. A single group's review covers the whole iteration; with multiple groups, an additional whole-iteration review runs only for material cross-group integration, dependency, shared-behaviour, or risk concerns.
- Coordinator is the normal interface to the user throughout the coordinated project loop. Workers and Reviewers return protected decisions, blockers, and manual-validation information to Coordinator instead of contacting the user directly. Coordinator advances through planning, execution, readiness review, completion, and the next iteration without requiring the user to name skills or approve procedural handoffs. It pauses only for approvals, protected decisions, ambiguous choices, or user-run validation, using an available question tool or a concise short-answer prompt.
- Markdown and Git remain the durable workflow state. PMD must not require a database, message queue, daemon, background worker system, or other custom orchestration service.

## Verification

- No repository commands are defined. Review Markdown changes directly and cross-check shared lifecycle rules across the affected skill, the installed instruction template, and `README.md` where user-facing behavior changes.
