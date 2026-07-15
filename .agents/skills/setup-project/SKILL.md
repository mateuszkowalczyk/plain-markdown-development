---
name: pmd:setup-project
description: Install or update Plain Markdown Development in a repository. Use when the user asks to set up, initialize, install, or upgrade PMD. Creates the documentation structure and safely inserts the PMD instruction block into the project's existing AGENTS.md.
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

1. Inspect the repository structure and its existing `AGENTS.md`, if present.
2. Create missing directories:

```text
docs/prd/
docs/specs/
docs/tasks/current/
docs/tasks/archived/
```

3. Create missing files:

```text
docs/inbox.md
docs/changelog.md
```

Create `docs/inbox.md` from `references/inbox.md`.

Use this minimal content for `docs/changelog.md`:

```markdown
# Changelog
```

4. Install the PMD instructions into the repository root `AGENTS.md`:

   - If `AGENTS.md` does not exist, create it with the complete marked block from `references/agents-instructions.md`.
   - If `AGENTS.md` exists and has no PMD markers, append the complete marked block after the existing content.
   - If both PMD markers already exist, replace only the content from `<!-- PMD:START -->` through `<!-- PMD:END -->` with the current template.
   - Preserve all content outside the PMD markers.
   - Never create a second PMD block.
   - Never replace the user's entire `AGENTS.md`.

5. If only one PMD marker exists, do not guess how to repair it. Report the malformed block and ask the user before editing `AGENTS.md`.
6. If the repository already has overlapping requirement or documentation directories:
   - preserve them
   - explain the overlap
   - ask before moving or renaming existing content
7. Report:
   - directories and files created
   - whether the PMD block was inserted, updated, or already current
   - any conflicts requiring user input

Do not create product requirements, specs, iterations, tasks, or changelog entries during setup unless explicitly asked.

## Idempotency

Running setup repeatedly must be safe:

```text
first run   → create structure and insert PMD block
later run   → update only the existing PMD block
```
