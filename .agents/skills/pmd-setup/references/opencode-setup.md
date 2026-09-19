# Configure the OpenCode runtime

Use this procedure only after confirming that the project uses OpenCode.

1. Inspect `.agents/pmd-runtime.md`, `.opencode/agents/`, and existing OpenCode configuration.
2. Ask whether to install or update the bundled runtime. Explain that it provides Coordinator, simple Builder, complex Builder, and one read-only Reviewer.
3. If declined, preserve all runtime and OpenCode files. An existing usable runtime remains valid; otherwise the main setup procedure collects alternative mappings.
4. If accepted, read every source file under `../assets/opencode/` and compare it with its target. Preserve usable identical files. Show differences and ask before replacing or materially merging divergent files whose local ownership or customization is uncertain.
5. Inspect exact model identifiers already configured in OpenCode and, when available, run `opencode models --verbose` without a provider filter. Use candidates from every available provider and inspect metadata for supported variants. Do not guess identifiers or variants.
6. Build a short cross-provider candidate list for each strength tier. Show two or three best-fitting candidates as `<display name> · <variant> (<provider>/<model>#<variant>) — <PMD tier>; <speed/cost note>`, omitting variant parts when none is selected. Mark uncertain classifications and do not recommend them by default.
7. Guide the user through one model choice for each tier, one question at a time:
   1. fast and economical — Simple Builder
   2. medium, predictable, and stable — Coordinator
   3. strong — Complex Builder
   4. very strong — Reviewer
   A model may be reused across tiers.
8. Present the exact role-to-model mapping and request one confirmation before editing files. Allow role-specific corrections and reconfirm the updated summary.
9. Install the four agent templates as `.opencode/agents/pmd-coordinator.md`, `pmd-builder-simple.md`, `pmd-builder-complex.md`, and `pmd-reviewer.md`, plus `.agents/pmd-runtime.md`. Add or update exactly one concrete `model: <provider>/<model>` frontmatter field in every agent, preserving an approved variant suffix.
10. After the new runtime files are ready, remove obsolete bundled `.opencode/agents/pmd-planner.md`, `pmd-worker-simple.md`, `pmd-worker-complex.md`, and `pmd-worker.md` if present. Delete a divergent or customized obsolete file only after showing it and receiving explicit approval; otherwise report that it was retained and that it may conflict with PMD naming.
11. Set `default_agent` to `pmd-coordinator` in existing OpenCode configuration while preserving unrelated settings and JSON/JSONC format. If no project configuration exists, create `opencode.json` with the OpenCode schema declaration and `"default_agent": "pmd-coordinator"`. Ask before replacing another configured default.
12. Validate that the four current agents exist, frontmatter parses, every agent has one approved concrete model, no placeholder remains, the runtime maps both Builder tiers correctly, Coordinator can invoke both Builders and the one Reviewer, and obsolete bundled Planner/Worker agents are absent or explicitly retained because the user declined deletion.
13. Report the final mapping and any retained obsolete file. Ask the user to restart OpenCode and select or confirm the configured Coordinator model when necessary.

Additional native or external Builder profiles may be added later as project-owned runtime customization.
