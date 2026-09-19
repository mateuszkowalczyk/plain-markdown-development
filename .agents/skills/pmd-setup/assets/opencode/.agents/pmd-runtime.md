# PMD Runtime

Coordinator reads this file semantically. Exact provider/model mappings live in each referenced OpenCode agent's `model` frontmatter field.

## Coordinator

Type: OpenCode primary agent
Agent: pmd-coordinator

## Builder profiles

### simple

Type: OpenCode subagent
Agent: pmd-builder-simple
Tier: simple
Capabilities: Localized, well-understood, low-risk planning, implementation, and direct validation.

### complex

Type: OpenCode subagent
Agent: pmd-builder-complex
Tier: complex
Capabilities: Cross-cutting, uncertain, high-risk, domain-heavy, or algorithmically demanding planning, implementation, and direct validation.

## Reviewer

Type: OpenCode subagent
Agent: pmd-reviewer

## Execution

Mode: Serial
Fallbacks: None. Report an unavailable assigned profile to the user.
Git isolation: Establish a reliable task-specific diff and final commit scope before invoking Builder. Builder must not stage files or create commits. Do not create planning or intermediate checkpoint commits; only approved `pmd-complete` Stage 2 creates the task's one final commit.
Project loop: Advance automatically through task creation, Builder, Reviewer, user validation, and `pmd-complete` Stage 1. After explicit archive approval, finish Stage 2 with one isolated task commit, then continue with the next clear task. Ask only for approval, a protected decision, an ambiguous choice, or user validation.
