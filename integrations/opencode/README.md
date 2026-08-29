# OpenCode v2 reference integration

This reference integration runs PMD with OpenCode as Coordinator while keeping the iteration, requirements, required project policy, and Git history as durable state. It adds no plugin, daemon, database, queue, or custom orchestration application.

The examples target the OpenCode 2 beta syntax verified on 2026-08-29. OpenCode v2 uses project agents under `.opencode/agents/`, Markdown bodies as agent instructions, and ordered `permissions` rules with the `shell` and `subagent` action names. Because v2 is still changing, compare the examples with the current [agents](https://opencode.ai/v2/docs/agents), [permissions](https://opencode.ai/v2/docs/permissions), and [CLI](https://opencode.ai/v2/docs/cli) documentation before adopting them.

## Included files

```text
.opencode/agents/pmd-coordinator.md
.opencode/agents/pmd-planner.md
.opencode/agents/pmd-worker.md
.opencode/agents/pmd-reviewer.md
examples/pmd-runtime.md
```

The four agent files are prompts and native OpenCode v2 configuration. The runtime file is human- and agent-readable Markdown that maps PMD roles and Worker profiles to runtime mechanisms.

## Install the reference configuration

The recommended path is to run `pmd-setup` and confirm that the project uses OpenCode. Setup summarizes and creates the required agent policy, asks whether to install the reference runtime, offers native-only or native-plus-Codex Worker profiles, and preserves existing configuration unless the user approves changes.

For manual installation:

1. Complete normal PMD installation first.
2. Merge this integration's `.opencode/agents/` directory into the consumer repository's `.opencode/agents/` directory. Preserve any existing OpenCode configuration.
3. Replace each `<provider>/<model>#<variant>` placeholder with a configured OpenCode model, or remove the `model` field to inherit the active model.
4. Copy `examples/pmd-runtime.md` to `.agents/pmd-runtime.md` and adapt the profile names, commands, and capability descriptions.
5. Create required `docs/agent-policy.md` from `.agents/skills/pmd-coordinate/references/agent-policy.md` and adapt it to the project without removing mandatory PMD approvals.
6. Commit the runtime configuration, agent configuration, and policy before planning the first execution group.
7. Start `opencode2`, select `pmd-coordinator` with `/agents`, and ask it to coordinate the project or a selected current iteration.

OpenCode reads agent files directly. PMD runtime Markdown is deliberately not parsed by a plugin: Coordinator reads it as instructions and uses the named subagent or synchronous command.

## Runtime roles

- `pmd-coordinator` is a primary agent. It guides the project across iterations, may update iteration state, invoke only the three PMD subagents, run the configured external Codex Worker, create local checkpoint commits, and transition automatically into completion and subsequent planning. Its prompt forbids technical implementation and restricts changelog/archive edits to the separately approved completion procedure.
- `pmd-planner` is a subagent that may update current iteration plans. Spec edits still require explicit user approval routed through Coordinator.
- `pmd-worker` is an OpenCode-native Worker profile. It may implement code and run direct checks but is denied edits to PMD state, requirements, specs, policy, and changelog.
- `pmd-reviewer` is a read-only subagent. Its permissions allow repository reads, the `pmd-review` skill, and Git inspection commands while denying edits and general shell execution.

Agent permissions are defense in depth, not the workflow source of truth. The PMD skills and repository instructions remain authoritative.

## Worker profiles

Planner discovers available Worker profiles by reading `.agents/pmd-runtime.md`. Each profile should describe the runtime and capabilities that matter for assignment. Planner selects a profile primarily from the execution group's expected difficulty and capability needs; Coordinator routes to exactly that profile.

The example includes:

- `native-default`, which invokes the OpenCode `pmd-worker` subagent
- `external-strong`, which invokes Codex CLI synchronously with `codex exec --sandbox workspace-write -C . -`

The labels are examples, not capability rankings built into PMD. Rename them and change their mappings freely. If only one profile is configured, Planner may assign it to every group. Do not silently fall back when an assigned runtime is unavailable unless the runtime file explicitly defines that fallback.

For an external CLI invocation, Coordinator supplies the complete Worker assignment on standard input and waits for the process to finish. The assignment includes the iteration path, execution-group and task IDs, relevant sources, acceptance criteria, manual-validation plan, and any current review findings. The CLI must return the semantic `pmd-worker` result contract before review begins. With the bundled permissions, invoke the example as `codex exec ... - <<'EOF'` rather than `cat <<'EOF' | codex exec ... -`: keeping `codex exec` at the start lets the narrow `codex exec *` shell rule match.

## Serial checkpoint workflow

The reference integration uses one working tree and one Worker at a time:

1. Before a group starts, run `git status --short`. Stop if unrelated changes would make the group diff ambiguous.
2. After Planner creates or revises the execution plan, create a planning checkpoint and confirm a sufficiently clean worktree before invoking Worker. Do not fold plan changes into the first implementation-group checkpoint.
3. Let Worker produce uncommitted implementation and test changes.
4. Let Reviewer inspect the complete diff against the current `HEAD`.
5. If Reviewer requests changes, return them to the same assigned Worker and repeat direct validation and review.
6. After Reviewer `PASS`, let Coordinator conduct every required manual-validation scenario one step at a time.
7. On manual-validation failure, repeat Worker → Reviewer → manual validation.
8. After all gates pass, let Coordinator mark the group's task checkboxes complete.
9. Stage only the accepted group changes and its iteration-state update, then create a local checkpoint commit such as `Complete E01 refresh implementation`.
10. Confirm the working tree is sufficiently clean before starting the next group.

Checkpoint commits are an MVP tradeoff that gives Reviewer a reliable diff boundary without worktrees. They are a rule of this reference integration, not a core PMD requirement. Do not run groups in parallel, retain background Worker sessions, push checkpoint commits automatically, or add a custom delegation plugin.

Reviewer is deliberately read-only in this reference configuration. It assesses the Worker's recorded direct checks, test coverage, source, and diff, but does not independently run arbitrary project commands. If review identifies missing or stale execution evidence, route that finding back to the assigned Worker for a fresh validation pass.

Make manual-validation instructions directly observable. When acceptance depends on the exit status or on distinguishing stdout from stderr, include a copy-pasteable capture command rather than asking the user to infer those details from ordinary console output. Project-generated validation artifacts such as Python `__pycache__` directories should be ignored or removed before clean-worktree and checkpoint gates.

After all groups complete, Coordinator requests a whole-iteration Reviewer pass and sets `Awaiting approval` only after `PASS`. It then starts `pmd-complete` Stage 1 automatically. That handoff is not archive approval: the completion skill performs a fresh readiness review and requests concise explicit approval before using its archive/changelog permissions. Approved completion ends with an isolated iteration commit. Coordinator then continues automatically with another clearly selected planned iteration or `pmd-plan`, pausing only for a required approval, decision, priority choice, or manual-validation result.

See the [MVP dogfood report](dogfood-report.md) for an end-to-end exercise of native and external Workers, replanning, review correction, manual validation, and completion.
