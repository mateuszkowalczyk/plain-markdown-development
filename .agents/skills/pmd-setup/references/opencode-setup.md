# Configure the OpenCode runtime

Use this procedure only after confirming that the project uses OpenCode.

1. Inspect `.agents/pmd-runtime.md` and `.opencode/agents/` before changing them.
2. Ask whether to install or update the bundled reference runtime. Explain briefly that it provides Coordinator, Planner, native Worker, and read-only Reviewer agents. Use a question tool when available or request a short yes/no answer.
3. If declined, preserve existing runtime and OpenCode files, then return to the main setup procedure. An existing usable runtime remains valid; otherwise the main procedure collects alternative runtime mappings.
4. If accepted, read every file under `../assets/opencode/` and compare it with existing targets:
   - preserve already usable files
   - ask before replacing or materially merging user-owned configuration
   - describe the proposed differences before requesting approval
5. Copy the four agent templates to `.opencode/agents/` and the runtime template to `.agents/pmd-runtime.md`, subject to the preservation rules above. Templates inherit the active OpenCode model; never invent provider or model identifiers.
6. Validate that all four `.opencode/agents/pmd-*.md` files exist, their frontmatter parses, no model placeholder remains, and the runtime exposes the configured profile names to Planner.

Additional native or external Worker profiles may be added later as project-owned runtime customization.
