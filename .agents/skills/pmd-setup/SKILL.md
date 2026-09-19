---
name: pmd-setup
description: Install or configure Plain Markdown Development. Use when the user asks to set up, initialize, install, or configure PMD. Creates the documentation structure, safely installs instructions, and configures the required agent policy and runtime. Use pmd-update for distribution version updates.
---

# Set up PMD

Initialize PMD without replacing existing project content or agent instructions.

## Resources and preflight

Read:

- `references/agents-instructions.md` for the complete marked instruction block
- `references/inbox.md` for a new inbox
- `../pmd-coordinate/references/agent-policy.md` for the default policy

Confirm the distribution contains `pmd-setup`, `pmd-update`, `pmd-builder`, `pmd-review`, `pmd-coordinate`, and `pmd-complete`, and that each skill frontmatter name matches its directory. If any are missing, report an incomplete distribution instead of synthesizing them.

When the project uses OpenCode, read and follow [references/opencode-setup.md](references/opencode-setup.md) while configuring its runtime.

## Procedure

1. Inspect the repository structure and existing root `AGENTS.md` and `CLAUDE.md` files.
2. For a new installation, ask whether to install PMD instructions in `AGENTS.md`, `CLAUDE.md`, or both. Do not edit either file before the user chooses. When PMD is already installed and only configuration is incomplete, preserve the existing selection.
3. Create missing directories:

```text
docs/prd/
docs/specs/
docs/tasks/current/
docs/tasks/archived/
```

4. Create missing `docs/inbox.md` from `references/inbox.md` and missing `docs/changelog.md` containing `# Changelog`.
5. Install instructions in each selected file:
   - create a missing file with the complete marked block
   - append the block when a file has neither marker
   - when both markers exist, preserve the block, report PMD as already installed there, and recommend `pmd-update` for distribution updates
   - preserve everything outside the markers, never duplicate the block, and leave every unselected instruction file unchanged
6. When an unselected instruction file contains a complete PMD block, preserve it and report it as potentially stale or intentionally shared. This is informational, not a conflict.
7. Stop and ask before editing a selected file with one unmatched marker. Also preserve and explain overlapping documentation or requirement directories, and ask before moving or renaming their content.
8. Configure the required policy and runtime below.
9. Report created files and directories, instruction-file selection and block state, preserved unselected blocks, policy/runtime/provider files created or preserved, configuration completeness, and unresolved conflicts.
10. Recommend restarting the agent or starting a new session, adding a brief product description under `docs/prd/`, and sending `pmd-coordinate`.

Do not create product requirements, specs, task files, or changelog entries unless explicitly asked. Do not add other orchestration directories to the minimal `docs/` structure.

## Required agent policy

PMD requires `docs/agent-policy.md`:

1. Read the default policy and inspect any existing project policy.
2. Summarize decisions reserved for the user and those delegated to Coordinator or Builder. Emphasize that spec changes and task completion always require explicit user approval.
3. Ask whether to keep the current/default boundaries or revise them. Prefer a structured question with “Keep defaults” recommended; otherwise request concise changes.
4. Create a missing policy from the default and apply approved adaptations. Preserve an existing policy byte-for-byte when the user keeps it. Never remove mandatory PMD approvals or overwrite a policy merely because setup is rerun.

## Required runtime

PMD is fully configured only when a usable `.agents/pmd-runtime.md` exists alongside the policy.

1. Inspect existing runtime and provider configuration. Detect OpenCode when possible; otherwise ask whether the project uses it.
2. For OpenCode, follow `references/opencode-setup.md`.
3. When no usable runtime exists afterward, inspect native delegation mechanisms and propose mappings that actually exist. Ask only for missing or ambiguous choices: Coordinator invocation, separate `simple` and `complex` Builder profiles, one read-only Reviewer invocation, role models when supported, serial execution, and diff isolation. Recommend a medium stable Coordinator, fast economical simple Builder, strong complex Builder, and very strong Reviewer. Never invent a CLI, provider, model, or fallback.
4. Validate that:
   - policy and runtime files exist and reflect the user's choices
   - runtime defines Coordinator, distinct usable Builder profiles for `simple` and `complex`, one Reviewer, and serial execution
   - every role has an explicit model mapping when the runtime supports per-role models
   - no existing user configuration was silently overwritten

If usable mappings remain unavailable, preserve completed policy work, report setup as incomplete, and state what runtime information is missing.

Setup must be idempotent: reruns preserve existing PMD blocks and user-owned policy/runtime unless the user approves a specific change.
