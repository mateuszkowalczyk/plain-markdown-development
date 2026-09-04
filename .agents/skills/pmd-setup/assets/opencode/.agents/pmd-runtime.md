# PMD Runtime

This file is read semantically by Coordinator. It is not an OpenCode configuration file and requires no custom parser. Exact provider/model mappings live in each referenced OpenCode agent's `model` frontmatter field.

## Coordinator

Type: OpenCode primary agent
Agent: pmd-coordinator

## Planner

Type: OpenCode subagent
Agent: pmd-planner

## Worker profiles

### simple

Type: OpenCode subagent
Agent: pmd-worker-simple
Difficulty: simple
Capabilities: Localized, well-understood, low-risk implementation and direct validation.

### complex

Type: OpenCode subagent
Agent: pmd-worker-complex
Difficulty: complex
Capabilities: Cross-cutting, uncertain, high-risk, domain-heavy, or algorithmically demanding implementation and direct validation.

## Reviewer

Type: OpenCode subagent
Agent: pmd-reviewer

## Execution

Mode: Serial
Fallbacks: None. Report an unavailable assigned profile to the user.
Git isolation: Use serial checkpoint commits after accepted execution groups.
Planning isolation: Commit every new or revised execution plan and confirm a sufficiently clean worktree before invoking the first affected Worker.
Project loop: Advance automatically through skill and role handoffs. After all reviews required by the iteration plan and implementation evidence pass, run `pmd-complete` Stage 1; request explicit archive approval, finish Stage 2 with an isolated iteration commit, then continue with the next clear iteration or `pmd-plan`. Ask only when approval, a protected decision, an ambiguous choice, or user validation is required.
