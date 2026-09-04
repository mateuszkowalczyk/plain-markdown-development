# Configure the OpenCode runtime

Use this procedure only after confirming that the project uses OpenCode.

1. Inspect `.agents/pmd-runtime.md` and `.opencode/agents/` before changing them.
2. Ask whether to install or update the bundled reference runtime. Explain briefly that it provides Coordinator, Planner, simple Worker, complex Worker, and one read-only Reviewer. Use a question tool when available or request a short yes/no answer.
3. If declined, preserve existing runtime and OpenCode files, then return to the main setup procedure. An existing usable runtime remains valid; otherwise the main procedure collects alternative runtime mappings.
4. If accepted, read every file under `../assets/opencode/` and compare it with existing targets:
   - preserve already usable files
   - ask before replacing or materially merging user-owned configuration
   - describe the proposed differences before requesting approval
5. Inspect exact model identifiers already configured in OpenCode and, when available, run `opencode models --verbose` without a provider filter. Use candidates from every provider available to the current project, including configured custom providers. Inspect catalog or configuration metadata for supported variants; do not guess model identifiers or variant names.
6. Build a short cross-provider candidate list for each PMD strength tier. Show two or three best-fitting candidates in the form `<display name> · <variant> (<provider>/<model>#<variant>) — <PMD tier>; <speed/cost note>`, omitting the variant parts when none is selected. Base recommendations on known capability and available metadata, not price alone. Mark uncertain classifications and do not recommend them by default. Offer the complete discovered list on request.
7. Guide the user through one choice for each tier. Use an available question tool for every choice when it can represent the candidates; fall back to a concise numbered prompt only when no suitable question tool is available:
   1. fast and economical — used by Simple Worker
   2. medium, predictable, and stable — used by Coordinator
   3. strong — used by Planner and Complex Worker
   4. very strong — used by Reviewer
   Ask one question at a time so the user chooses a model rather than typing an identifier. A chosen model may be reused across tiers. Planner may instead use the very-strong choice for unusually difficult or ambiguous projects.
8. Present the resulting role-to-model mapping, including exact identifiers and variants, then ask for one confirmation before editing files. Use an available question tool for this confirmation too, with a concise yes/no prompt only as fallback. Allow concise role-specific corrections; re-ask only the affected tier or role and show the updated summary before requesting confirmation again.
9. Copy the five agent templates to `.opencode/agents/` and the runtime template to `.agents/pmd-runtime.md`, subject to the preservation rules above. Add or update exactly one `model: <provider>/<model>` frontmatter field in each installed agent using the approved mapping. Preserve an approved variant suffix such as `#high`. Remove the obsolete bundled `pmd-worker.md` after the two replacements are ready; ask first if that legacy file has user customizations.
10. Validate that:
   - `pmd-coordinator`, `pmd-planner`, `pmd-worker-simple`, `pmd-worker-complex`, and `pmd-reviewer` exist and their frontmatter parses
   - every agent has one concrete approved `model` value and no placeholder remains
   - the runtime maps `simple` and `complex` to their matching Worker agents
   - Coordinator can invoke both Workers but only one Reviewer agent exists
11. Report the final role-to-model mapping. Because an existing OpenCode primary session may retain its selected session model, ask the user to select or confirm the configured Coordinator model in that session when necessary.

Additional native or external Worker profiles may be added later as project-owned runtime customization.
