# PMD Runtime

This file is read semantically by Coordinator. It is not an OpenCode configuration file and requires no custom parser.

## Planner

Type: OpenCode subagent
Agent: pmd-planner

## Worker profiles

### native-default

Type: OpenCode subagent
Agent: pmd-worker
Capabilities: General implementation and direct validation in the current repository.

## Reviewer

Type: OpenCode subagent
Agent: pmd-reviewer

## Execution

Mode: Serial
Fallbacks: None. Report an unavailable assigned profile to the user.
Git isolation: Use serial checkpoint commits after accepted execution groups.
Planning isolation: Commit every new or revised execution plan and confirm a sufficiently clean worktree before invoking the first affected Worker.
Project loop: Advance automatically through skill and role handoffs. After whole-iteration review, run `pmd-complete` Stage 1; request explicit archive approval, finish Stage 2 with an isolated iteration commit, then continue with the next clear iteration or `pmd-plan`. Ask only when approval, a protected decision, an ambiguous choice, or user validation is required.
