---
name: pmd-plan
description: Plan or replan a coordinated Markdown iteration from selected requirements. Use only when PMD Coordinator delegates creation, scoping, or technical replanning. Return protected decisions through Coordinator, obtain explicit approval before changing specs, and never implement code.
---

# Plan an iteration

Create an iteration containing only the selected scope and a complete execution plan for Coordinator.

Planner owns the technical implementation plan: task decomposition, dependencies, execution grouping, difficulty classification, acceptance criteria, manual-validation design, Worker-profile assignment, and the whole-iteration review decision. Planner does not implement code or own execution state.

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
4. Use the fewest coherent execution groups that preserve implementation context, dependency order, the correct Worker difficulty tier, and useful review or manual-validation boundaries. One group may contain one task, several tasks, or the entire iteration; task granularity does not dictate agent-invocation granularity. Do not create one group per task by default.
5. Merge adjacent work that uses the same Worker tier unless a real dependency, risk boundary, manual-validation boundary, or materially different implementation context justifies separation.
6. Record dependencies between execution groups. Dependencies reference group IDs, not prose descriptions.
7. Classify every execution group as `simple` or `complex`:
   - `simple` — localized, well-understood, low-risk work with limited interactions
   - `complex` — cross-cutting, uncertain, high-risk, domain-heavy, or algorithmically demanding work
   - when a coherent group contains mixed work that is not worth splitting, classify the whole group as `complex`
8. Assign exactly one available Worker profile suitable for that difficulty to every execution group. Runtime configuration owns the profile names and their difficulty mappings. If no suitable configured profile exists, report planning as blocked instead of silently substituting another tier.
9. Add a concise `Reason` only when the profile choice or grouping is not self-explanatory.
10. Decide whether an additional whole-iteration review is required after all group reviews:
   - `not required` for a single group because its group review covers the whole implementation
   - for multiple groups, `required` only when dependencies, shared behaviour, cross-group integration, high risk, or another material whole-system concern warrants it
   - multiple groups alone are not sufficient reason for another review
11. Define acceptance criteria where the task text and authoritative sources do not already make success sufficiently clear.
12. Add per-group manual-validation steps when user action is needed to verify behaviour. State observable expected results.

Example structure:

```markdown
## Tasks

### <Sensible group>

- [ ] T01 — <Small, verifiable task>
- [ ] T02 — <Small, verifiable task>

## Execution plan

### E01 — <Coherent execution group>

Tasks: T01, T02
Difficulty: simple | complex
Worker profile: <configured-profile>
Depends on: —
Reason: <optional reason>

## Review plan

Whole-iteration review: required | not required
Reason: <required only when another whole-iteration review is required>

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

Do not invent a Worker profile that is absent from runtime configuration. If a necessary capability or difficulty tier is unavailable, report the plan as blocked instead of silently mapping the work to a different profile.

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
- every execution group has a valid `simple` or `complex` classification and names an available Worker profile mapped to that difficulty
- execution order is actionable from the recorded dependencies
- the plan states whether another whole-iteration review is required and gives a material reason when it is
- required manual validation is specific enough for Coordinator to conduct step by step

An iteration does not need a single unifying goal.
