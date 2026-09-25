---
name: pmd-update
description: Update an existing Plain Markdown Development installation to the latest or a user-selected distribution version while preserving project documentation, non-PMD instructions, and detected runtime customizations. Use pmd-setup for a new installation.
---

# Update PMD

Synchronize an existing installation with a trusted PMD source without changing consumer product documentation.

## Managed scope

The core update may change only:

- PMD-owned directories matching `.agents/skills/pmd-*`
- the complete PMD instruction block between `<!-- PMD:START -->` and `<!-- PMD:END -->` in root `AGENTS.md`

Preserve all `AGENTS.md` content outside the markers, everything under `docs/`, non-PMD skills, and other instruction files.

`.agents/pmd-runtime.md`, `.opencode/agents/`, and OpenCode configuration are project runtime configuration, not automatically replaced by the core update. Synchronize them with bundled OpenCode assets only through the explicitly accepted runtime step below.

## Update source

Use a source directory or version supplied by the user. Otherwise obtain the current default branch of:

`https://github.com/mateuszkowalczyk/plain-markdown-development`

Fetch remote content into a temporary location and record a source commit when available. Never execute scripts from an unverified source.

Before changing the target, require:

- exactly one complete marker block in `.agents/skills/pmd-setup/references/agents-instructions.md`
- valid `.agents/skills/pmd-*/SKILL.md` files whose frontmatter names match their directories
- core skills `pmd-setup`, `pmd-update`, `pmd-builder`, `pmd-review`, `pmd-coordinate`, and `pmd-complete`
- no source `pmd-plan` or `pmd-worker` directory

Stop without modification if validation fails.

## Procedure

1. Confirm PMD is already installed from existing PMD skills or a complete root `AGENTS.md` instruction block. Otherwise recommend `pmd-setup`.
2. Resolve and validate the update source.
3. Inspect PMD-managed files for incomplete or duplicate markers and uncommitted changes. Stop and ask how to resolve malformed markers in root `AGENTS.md`.
4. Show PMD-managed files with detected local changes that replacement or deletion would discard and ask for confirmation. When version control cannot distinguish a committed customization from an ordinary older distribution version, explain that limitation and ask before overwriting a divergent file.
5. Stage the complete skill-directory replacement when possible, then:
   - add skills introduced by the source
   - replace installed skills present in the source as complete directories so removed upstream files do not remain
   - preserve non-PMD skills
   - remove obsolete managed `pmd-plan` and `pmd-worker` directories when they have no detected customization
   - when either obsolete directory has detected customization, show it and request explicit deletion approval; if approval is withheld, retain it and report the update as incomplete
   - for any other installed `pmd-*` skill absent from the source, report it and ask before deletion
6. If root `AGENTS.md` contains one complete PMD block, replace exactly that range with the source block. If it has no PMD block, leave it unchanged, report the installation as incomplete, and recommend `pmd-setup` to install the block.
7. Do not create, edit, move, or delete anything under `docs/`.
8. Inspect `.agents/pmd-runtime.md` and `.opencode/agents/`. When they use bundled Planner/Worker names or the user requests the bundled OpenCode runtime, offer to synchronize them with the source assets under `pmd-setup/assets/opencode/`.
9. If bundled runtime synchronization is accepted:
   - compare every current target with the new bundled file and show material differences
   - preserve exact approved model identifiers and variants while updating role content and mappings
   - ask before overwriting any detected or uncertain local customization
   - install current Coordinator, simple Builder, complex Builder, Reviewer, and runtime files
   - remove obsolete bundled `pmd-planner.md`, `pmd-worker-simple.md`, `pmd-worker-complex.md`, and `pmd-worker.md` after the replacements are ready
   - if an obsolete agent has detected customization, request explicit deletion approval; if withheld, retain it and report runtime synchronization as incomplete
   - preserve unrelated OpenCode agents and settings
10. Verify that source PMD skill directories match installed copies; any updated `AGENTS.md` block matches the source; outside-marker content and unrelated files are unchanged; and no unapproved customization was overwritten.
11. When bundled runtime synchronization was accepted, also verify that Builder mappings and agents are usable, exactly one Reviewer exists, obsolete bundled Planner/Worker assets are absent unless explicitly retained, and the OpenCode default remains `pmd-coordinator`.
12. Report the source path/version/commit; skills added, updated, removed, or retained; whether the `AGENTS.md` block was updated; runtime files synchronized or preserved; and every unresolved conflict.
13. Confirm that the root `AGENTS.md` block, `docs/agent-policy.md`, and a usable `.agents/pmd-runtime.md` exist. If any is missing or the runtime still maps removed roles, report installation as incomplete and recommend `pmd-setup` without creating or changing project configuration.
14. Recommend restarting the agent or starting a new session.

## Safety

An update request authorizes replacing uncustomized PMD-managed distribution files. It does not authorize discarding detected local customizations or replacing project runtime configuration without the runtime confirmation above.

If application fails partway through, restore safely backed-up managed files when possible; otherwise stop and report the exact partial state rather than attempting speculative repairs.
