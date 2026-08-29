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

### external-strong

Type: CLI
Command: `codex exec --sandbox workspace-write -C . -`
Input: Pass the complete Worker assignment through standard input.
Model: Configure in Codex or add `--model <model>` to the command.
Capabilities: Work requiring the external CLI's configured coding and reasoning capability.

## Reviewer

Type: OpenCode subagent
Agent: pmd-reviewer

## Execution

Mode: Serial
External CLI: Synchronous; wait for its final result before invoking Reviewer.
Fallbacks: None. Report an unavailable assigned profile to the user.
Git isolation: Use serial checkpoint commits after accepted execution groups.
