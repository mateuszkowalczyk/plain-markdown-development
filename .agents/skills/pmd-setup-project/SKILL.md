---
name: pmd-setup-project
description: Install or update Plain Markdown Development in a repository. Use when the user asks to set up, initialize, install, or upgrade PMD. Creates the documentation structure and safely inserts the PMD instruction block into the user's chosen AGENTS.md or CLAUDE.md file.
---

# Set up PMD

Initialize or update PMD without replacing the user's existing agent instructions.

## Required templates

Read:

`references/agents-instructions.md`

`references/inbox.md`

The first contains the complete PMD instruction block between:

```text
<!-- PMD:START -->
<!-- PMD:END -->
```

## Procedure

1. Inspect the repository structure and its existing `AGENTS.md` and `CLAUDE.md`, if present.
2. Ask whether to install the PMD instructions in `AGENTS.md` or `CLAUDE.md`:
   - use the question tool when it is available
   - otherwise ask the user directly
   - do not modify either instruction file until the user chooses one
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

5. Install the PMD instructions into the selected repository-root instruction file:

   - If the selected file does not exist, create it with the complete marked block from `references/agents-instructions.md`.
   - If the selected file exists and has no PMD markers, append the complete marked block after the existing content.
   - If both PMD markers already exist, replace only the content from `<!-- PMD:START -->` through `<!-- PMD:END -->` with the current template.
   - Preserve all content outside the PMD markers.
   - Never create a second PMD block.
   - Never replace the user's entire instruction file.
   - Leave the unselected instruction file unchanged.

6. If only one PMD marker exists in the selected file, do not guess how to repair it. Report the malformed block and ask the user before editing that file.
7. If the repository already has overlapping requirement or documentation directories:
   - preserve them
   - explain the overlap
   - ask before moving or renaming existing content
8. Report:
   - directories and files created
   - which instruction file was selected
   - whether its PMD block was inserted, updated, or already current
   - any conflicts requiring user input
9. After setup finishes without unresolved conflicts, recommend this sequence:
   1. restart the agent or start a new session so it reloads the selected instruction file
   2. briefly describe the app requirements in a Markdown file under `docs/prd/`
   3. use `pmd-plan-iteration` to plan the first or next iteration from those requirements

Do not create product requirements, specs, iterations, tasks, or changelog entries during setup unless explicitly asked.

## Idempotency

Running setup repeatedly must be safe:

```text
first run   → create structure and insert the PMD block in the selected instruction file
later run   → update only the existing PMD block in the selected instruction file
```
