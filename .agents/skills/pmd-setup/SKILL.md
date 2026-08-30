---
name: pmd-setup
description: Install or configure Plain Markdown Development. Use when the user asks to set up, initialize, install, or configure PMD. Creates the documentation structure, safely installs instructions, and configures the required agent policy and runtime. Use pmd-update for distribution version updates.
---

# Set up PMD

Initialize PMD without replacing the user's existing agent instructions.

## Required templates

Read:

`references/agents-instructions.md`

`references/inbox.md`

Also read the default policy from the sibling skill:

`../pmd-coordinate/references/agent-policy.md`

For OpenCode runtime setup, read the bundled files under:

`assets/opencode/`

`references/agents-instructions.md` contains the complete PMD instruction block between:

```text
<!-- PMD:START -->
<!-- PMD:END -->
```

The distributed `.agents/skills/` directory must include:

```text
pmd-setup
pmd-update
pmd-plan
pmd-worker
pmd-review
pmd-coordinate
pmd-complete
```

Confirm these sibling skills are present before setup. If any are missing, report an incomplete PMD distribution instead of synthesizing them in the consumer repository.

## Procedure

1. Inspect the repository structure and its existing `AGENTS.md` and `CLAUDE.md`, if present.
2. Ask whether to install the PMD instructions in `AGENTS.md`, `CLAUDE.md`, or both:
   - use the question tool when it is available
   - otherwise ask the user directly
   - installing in both supports teams whose agents use different instruction-file conventions
   - one file may instead contain the PMD block and the other may explicitly direct agents to read it
   - do not modify either instruction file until the user chooses
   - after the user chooses, inspect both files for PMD markers
   - when PMD is already installed and the request is only to complete its coordination configuration, preserve the existing instruction-file selection and do not ask the user to choose it again
3. Create missing directories:

```text
docs/prd/
docs/specs/
docs/tasks/current/
docs/tasks/archived/
```

4. Create missing files:

```text
docs/inbox.md
docs/changelog.md
```

Create `docs/inbox.md` from `references/inbox.md`.

Use this minimal content for `docs/changelog.md`:

```markdown
# Changelog
```

5. Install the PMD instructions into each selected repository-root instruction file:

   - If a selected file does not exist, create it with the complete marked block from `references/agents-instructions.md`.
   - If a selected file exists and has no PMD markers, append the complete marked block after the existing content.
   - If both PMD markers already exist, leave the block unchanged, report that PMD is already installed in that file, and recommend `pmd-update` when the user wants to update it.
   - Preserve all content outside the PMD markers.
   - Never create a second PMD block in either file.
   - Never replace the user's entire instruction file.
   - When only one file is selected, leave the other instruction file unchanged.

6. When only one instruction file is selected and the other contains a complete PMD block:
   - leave it unchanged
   - report that its PMD block may be stale or may intentionally support another agent
   - treat the notice as informational, not as an unresolved conflict
7. If only one PMD marker exists in any selected file, do not guess how to repair it. Report the malformed block and ask the user before editing that file.
8. If the repository already has overlapping requirement or documentation directories:
   - preserve them
   - explain the overlap
   - ask before moving or renaming existing content
9. Follow **Configure coordinated PMD** below. PMD has one coordinated workflow and requires both `docs/agent-policy.md` and usable runtime configuration.
10. Report:
   - directories and files created
   - which instruction file or files were selected
   - whether each selected PMD block was inserted or already present
   - any potentially stale PMD block found in the unselected instruction file
   - whether coordinated configuration was completed or left incomplete
   - policy, runtime, and provider-specific files created or preserved
   - any conflicts requiring user input
11. After setup finishes without unresolved conflicts, recommend this sequence:
   1. restart the agent or start a new session so it reloads the selected instruction file
   2. briefly describe the app requirements in a Markdown file under `docs/prd/`
   3. ask the agent to coordinate the project; Coordinator will invoke planning itself when needed

Do not create product requirements, specs, iterations, tasks, or changelog entries during setup unless explicitly asked. Do not add other directories to the minimal consumer `docs/` structure for orchestration.

## Configure coordinated PMD

PMD configuration is complete only when both `docs/agent-policy.md` and a usable `.agents/pmd-runtime.md` exist. Do not report PMD as configured when either is missing.

### Configure the required agent policy

1. Inspect `docs/agent-policy.md` before changing it.
2. Read `../pmd-coordinate/references/agent-policy.md` and summarize its default boundaries in plain language:
   - the user decides product behaviour and scope, lasting architecture tradeoffs, public API or persistent-data changes, security/privacy decisions, destructive actions, and material external cost
   - Planner decides implementation architecture, decomposition, dependencies, Worker assignment, and validation strategy within approved boundaries
   - Worker decides local implementation details, internal names, straightforward refactors, and concrete tests within its assignment
   - mandatory spec-change and completion approvals always remain with the user
3. If no project policy exists, ask whether to keep those defaults or change them. Prefer a structured question with a recommended "Keep defaults" choice; otherwise ask for a short answer. When changes are requested, ask only for concise additions, removals, or authority moves.
4. Create `docs/agent-policy.md` from the default and apply the user's requested adaptations. Do not remove PMD's mandatory spec-change or completion-approval rules.
5. If a policy already exists, summarize its current boundaries and ask whether to keep or revise it. Preserve it byte-for-byte when the user chooses to keep it. Never overwrite it merely because setup is rerun.

### Configure runtime

1. Inspect the repository for an existing `.agents/pmd-runtime.md`, `.opencode/`, or OpenCode configuration. Ask whether the user uses OpenCode when that cannot be determined reliably.
2. When the user uses OpenCode, ask whether to install or update the bundled reference runtime:
   - briefly explain that it provides Coordinator, Planner, native Worker, and read-only Reviewer agents
   - use an available question tool or ask for a short yes/no answer
3. If the user accepts OpenCode runtime setup:
   - read every file under `assets/opencode/`
   - copy the four agent templates to `.opencode/agents/`
   - copy the runtime template to `.agents/pmd-runtime.md`
   - the bundled agent templates inherit the active OpenCode model; do not invent provider or model identifiers
   - explain that additional native or external Worker profiles may be added later as project-owned runtime customization
4. If target OpenCode agent files or `.agents/pmd-runtime.md` already exist, compare them with the proposed configuration. Preserve them when already usable. Ask before replacing or materially merging user-owned configuration, and describe exactly what would change.
5. If the user does not use OpenCode, inspect the available native delegation or agent mechanisms and propose a minimal `.agents/pmd-runtime.md` from capabilities that are actually available. Ask the user only for missing or ambiguous semantic mappings: Planner invocation, at least one named Worker profile with capabilities and invocation, Reviewer invocation, serial execution, and diff/checkpoint isolation. Do not invent a CLI, provider, model, or fallback.
6. If an OpenCode user declines reference runtime setup and no usable runtime already exists, ask for the missing semantic runtime mappings as for other environments. If the user cannot provide them, preserve the configured policy but report setup as incomplete and state that `.agents/pmd-runtime.md` is still required. Declining the bundled runtime does not invalidate an existing usable runtime.
7. Validate completed setup:
   - `docs/agent-policy.md` exists and reflects the user's choices
   - `.agents/pmd-runtime.md` defines Planner, at least one Worker profile, Reviewer, and serial execution
   - configured profile names are usable by Planner
   - for OpenCode, all four `.opencode/agents/pmd-*.md` files exist, their frontmatter parses, and no model placeholder remains
   - no existing user configuration was silently overwritten

## Idempotency

Running setup repeatedly must be safe:

```text
first run   → create structure, install instructions, and configure policy and runtime
later run   → preserve the existing PMD block and user-owned policy/runtime unless the user approves changes
```
