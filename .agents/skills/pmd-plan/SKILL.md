---
name: pmd-plan
description: Plan or replan a coordinated Markdown iteration from selected requirements. Use only when PMD Coordinator delegates creation, scoping, or technical replanning. Return protected decisions through Coordinator, obtain explicit approval before changing specs, and never implement code.
---

# Plan an iteration

Create an iteration containing only the selected scope and a complete execution plan for Coordinator.

Planner owns the technical implementation plan: task decomposition, dependencies, execution grouping, acceptance criteria, manual-validation design, and Worker-profile assignment. Planner does not implement code or own execution state.

## Shared planning procedure

1. Inspect `docs/tasks/current/` and `docs/tasks/archived/` to determine the next iteration number, or locate the current iteration when replanning.
2. Read the PRDs, specs, inbox entries, bug context, repository instructions, code, and tests relevant to the selected scope.
3. Require and read `docs/agent-policy.md`. If it is missing, report planning as blocked and direct setup through `pmd-setup`; do not assume decision boundaries.
4. Decide whether each item needs a spec:
   - meaningful product behaviour should normally have a spec
   - small bugs, maintenance, refactors, research, and technical work may go directly into an iteration
5. When required behaviour is missing or unclear:
   - discuss it with the user through Coordinator
   - use the discussion, requirements, and repository context to draft the spec
   - present the proposed creation, change, deletion, or rename
   - obtain explicit user approval before modifying any file in `docs/specs/`
6. Convert the selected scope into small, independently verifiable checkboxes.
7. Group checkboxes in the way that best communicates the work, such as by feature, subsystem, or work type.
8. Place bugs either in a separate section or beside the feature they affect, whichever is clearer.
9. Create or update `docs/tasks/current/iteration-NNN-short-name.md` and set a new iteration's status to `Planned`.
10. Remove an item from `docs/inbox.md` only after it is fully represented in a spec or the iteration.
11. Summarize the iteration, blockers, protected decisions, and any approved replanning changes.

Do not implement code. Spec-change approval permits only the proposed spec mutation and is separate from completion approval handled by `pmd-complete`.

## Execution plan

1. Read `.agents/pmd-runtime.md` or the runtime configuration identified by Coordinator. If no usable runtime exists, report planning as blocked and direct setup through `pmd-setup`.
2. Identify the configured Worker profiles and their documented capabilities. Runtime configuration owns profile-to-CLI, provider, and model mappings.
3. Give every task a stable `TNN` ID and place every task in exactly one execution group with a stable `ENN` ID.
4. Group tasks by coherent implementation context. One group may contain one task, several tasks, or the entire iteration; task granularity does not dictate agent-invocation granularity.
5. Record dependencies between execution groups. Dependencies reference group IDs, not prose descriptions.
6. Assign exactly one available Worker profile to every execution group. Choose primarily from expected difficulty and required reasoning, coding, or domain capability. Use the same profile for all groups when only one is available.
7. Add a concise `Reason` only when the profile choice or grouping is not self-explanatory.
8. Define acceptance criteria where the task text and authoritative sources do not already make success sufficiently clear.
9. Add per-group manual-validation steps when user action is needed to verify behaviour. State observable expected results.

Example structure:

```markdown
## Tasks

### <Sensible group>

- [ ] T01 — <Small, verifiable task>
- [ ] T02 — <Small, verifiable task>

## Execution plan

### E01 — <Coherent execution group>

Tasks: T01, T02
Worker profile: <configured-profile>
Depends on: —
Reason: <optional reason>

## Manual validation

### E01

1. <Actionable user step>

Expected: <observable result>
```

Task and execution-group IDs are stable within an iteration:

- never renumber or reuse existing IDs during replanning
- give newly added tasks or groups the next unused ID
- retain an existing group ID when its purpose remains substantially the same
- when replacing or splitting a group, remove the obsolete group and use new IDs for genuinely new groups

Do not invent a Worker profile that is absent from runtime configuration. If a necessary capability is unavailable, report the plan as blocked instead of silently mapping the work to a different profile.

When planning is complete, suggest `pmd-coordinate`.

## Replanning

Replan when Coordinator reports an invalid technical premise, dependency, grouping, acceptance criterion, manual-validation plan, or Worker capability assignment.

Planner may revise implementation strategy, task decomposition, execution grouping, dependencies, test strategy, and Worker-profile assignment without user approval only when approved product behaviour, specs, and iteration scope remain unchanged. Preserve completed task state and stable IDs.

If replanning would change intended behaviour, modify a spec, expand scope, remove required scope, or make a decision protected by `docs/agent-policy.md`, return the decision through Coordinator and wait for explicit approval where required.

## Readiness test

Every iteration is ready only when:

- every task is understandable and verifiable
- meaningful feature behaviour is sufficiently specified
- blockers and protected decisions are visible
- tasks are reasonably small
- selected scope is distinguishable from deferred work

An iteration is ready only when it also satisfies all of the following:

- task IDs and execution-group IDs are present, unique, and stable
- `docs/agent-policy.md` exists and defines project decision boundaries
- every task belongs to exactly one execution group
- every execution group contains at least one task
- all dependency references resolve and the dependency graph has no cycle
- every execution group names an available Worker profile
- execution order is actionable from the recorded dependencies
- required manual validation is specific enough for Coordinator to conduct step by step

An iteration does not need a single unifying goal.
