---
name: pmd-setup
description: Install or configure Plain Markdown Development. Use when the user asks to set up, initialize, install, or configure PMD. Creates the documentation structure, safely installs instructions, and configures the required agent policy and runtime. Use pmd-update for distribution version updates.
---

# Set up PMD

Initialize PMD without replacing existing project content or agent instructions.

## Resources and preflight

Read:

- `references/agents-instructions.md` for the complete block between `<!-- PMD:START -->` and `<!-- PMD:END -->`
- `references/inbox.md` for a new inbox
- `../pmd-coordinate/references/agent-policy.md` for the required default policy

Confirm the distribution contains `pmd-setup`, `pmd-update`, `pmd-plan`, `pmd-worker`, `pmd-review`, `pmd-coordinate`, and `pmd-complete`. If any are missing, report an incomplete distribution instead of synthesizing them.

When the project uses OpenCode, read and follow [references/opencode-setup.md](references/opencode-setup.md) only while configuring its runtime.

## Procedure

1. Inspect the repository structure and existing root `AGENTS.md` and `CLAUDE.md` files.
2. For a new installation, ask whether to install PMD instructions in `AGENTS.md`, `CLAUDE.md`, or both. Use a question tool when available and do not edit either file before the user chooses, then inspect both files for PMD markers. Installing both supports mixed agent conventions; one file may instead direct agents to the other. When PMD is already installed and only configuration is incomplete, preserve the existing selection without asking again.
3. Create missing directories:

```text
docs/prd/
docs/specs/
docs/tasks/current/
docs/tasks/archived/
```

4. Create missing `docs/inbox.md` from `references/inbox.md` and missing `docs/changelog.md` with:

```markdown
# Changelog
```

5. Install instructions in each selected file:
   - create a missing file with the complete marked block
   - append the block when a file has neither marker
   - when both markers exist, preserve the block, report PMD as already installed there, and recommend `pmd-update` for distribution updates
   - preserve everything outside the markers, never duplicate the block, and leave every unselected instruction file unchanged
6. When an unselected instruction file contains a complete PMD block, preserve it and report it as potentially stale or intentionally shared. This is informational, not a conflict.
7. Stop and ask before editing a selected file with one unmatched marker. Also preserve and explain overlapping documentation or requirement directories, and ask before moving or renaming their content.
8. Configure the required policy and runtime below.
9. Report created files and directories, instruction-file selection and block state, preserved unselected blocks, policy/runtime/provider files created or preserved, configuration completeness, and unresolved conflicts.
10. When setup completes, recommend restarting the agent or starting a new session, adding a brief product description under `docs/prd/`, and sending `pmd-coordinate`.

Do not create product requirements, specs, iterations, tasks, or changelog entries unless explicitly asked. Do not add other orchestration directories to the minimal `docs/` structure.

## Required agent policy

PMD requires `docs/agent-policy.md`:

1. Read the default policy and inspect any existing project policy.
2. Summarize which decisions the default reserves for the user and delegates to Planner or Worker. Emphasize that spec changes and iteration completion always require user approval.
3. Ask whether to keep the current/default boundaries or revise them. Prefer a structured question with “Keep defaults” recommended; otherwise request concise additions, removals, or authority changes.
4. Create a missing policy from the default and apply approved adaptations. Preserve an existing policy byte-for-byte when the user keeps it. Never remove mandatory PMD approvals or overwrite a policy merely because setup is rerun.

## Required runtime

PMD is fully configured only when a usable `.agents/pmd-runtime.md` exists alongside the policy.

1. Inspect existing runtime and provider configuration. Detect OpenCode when possible; otherwise ask whether the project uses it.
2. For OpenCode, follow `references/opencode-setup.md`.
3. When no usable runtime exists afterward, inspect available native delegation or agent mechanisms and propose a minimal runtime from capabilities that actually exist. Ask only for missing or ambiguous mappings: Planner invocation, at least one named Worker profile with capabilities and invocation, Reviewer invocation, serial execution, and diff/checkpoint isolation. Never invent a CLI, provider, model, or fallback.
4. Validate that:
   - policy and runtime files exist and reflect the user's choices
   - runtime defines Planner, at least one usable Worker profile, Reviewer, and serial execution
   - no existing user configuration was silently overwritten

If usable mappings remain unavailable, preserve any completed policy work, report setup as incomplete, and state what runtime information is missing.

Setup must be idempotent: reruns preserve existing PMD blocks and user-owned policy/runtime unless the user approves a specific change.
