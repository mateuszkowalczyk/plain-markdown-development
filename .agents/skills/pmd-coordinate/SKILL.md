---
name: pmd-coordinate
description: Guide an ongoing PMD project across task creation, tiered building, independent review, completion, and subsequent tasks. Use for user requests to plan, implement, review, finish, approve, close, archive, or continue PMD work. Never implement technical work directly or bypass approval gates.
---

# Coordinate a PMD project

Own the user-facing workflow and durable state across PMD tasks. One task is the complete delivery unit: one coherent outcome, one Builder context, one Builder tier, one complete-diff review, one completion approval, and one final commit.

Advance through procedural handoffs automatically when no approval, protected decision, ambiguous choice, or user-run validation is required. Do not ask the user to name a PMD skill or restate a clear objective. Core PMD defines role contracts and Markdown state, not a particular CLI, provider, model, or invocation syntax.

## Start and preflight

1. Read repository instructions, `docs/agent-policy.md`, and `.agents/pmd-runtime.md`. If policy or usable runtime is missing, stop coordinated work and use `pmd-setup`.
2. Inspect `docs/tasks/current/` and `docs/tasks/archived/`. Select the task named by the user or the next clearly prioritized actionable task. Ask only when multiple choices are materially different. If no current task exists and the next scope is clear, continue to Create a task.
3. Read the selected task and its sources. Confirm its dependencies are completed archived task files and its assigned Builder profile exists. Continue an `Open` task through Builder; route an `Awaiting approval` task directly to `pmd-complete` Stage 1; treat `Completed` in the current directory as invalid state.
4. Check for unrelated changes or another condition that makes the task diff ambiguous. Apply the runtime's isolation procedure; stop if reliable review and completion scope cannot be established.
5. Confirm the task is sized for one Builder context and one review boundary. If it is not, split it before implementation. Each resulting file is an independent task with its own tier, review, approval, and commit.

Do not silently substitute a profile, provider, model, or runtime. If a delegated agent fails operationally, preserve state, report the affected role and error, retry the same configured role only when the cause is plausibly transient, and never infer success from a partial response.

## Create a task

When no current task exists and the next scope is clear, Coordinator creates it directly from the user's request, approved requirements, specs, inbox, and repository context. Technical implementation planning belongs to Builder and must not be duplicated here.

Choose a number greater than the highest `NNN` in every historical or current `task-NNN-*` and `iteration-NNN-*` filename. Create `docs/tasks/current/task-NNN-short-name.md` with exactly this schema and section order:

```markdown
# Task NNN — Short name

Status: Open
Builder tier: simple | complex
Sources: <paths, or None>
Depends on: <archived task paths, or None>

## Outcome

<one coherent delivered outcome>

## Acceptance

- <observable condition>

## Plan

Pending

## Validation

Pending

## Review

Pending

## Manual validation

None
```

Use the same schema for every task. Acceptance items are plain bullets, not nested tasks or checkboxes. Dependencies exist only between task files. Multiple current tasks are allowed, but each must stand alone as a delivery unit.

Assign `simple` when the implementation is localized, well-understood, low-risk, and directly testable. Assign `complex` when it is cross-cutting, uncertain, high-risk, domain-heavy, or algorithmically demanding. Use the stronger tier for the whole task when inseparable work contains both. Split work when it has independently deliverable outcomes, needs different tiers, needs different review or approval boundaries, or cannot be implemented and reviewed confidently in one context.

Coordinator owns Outcome, Acceptance, Sources, Depends on, Builder tier, Review, Manual validation, and Status. Builder may edit only Plan and Validation. A scope or behaviour decision protected by policy requires user input, and any spec creation, edit, rename, or deletion requires explicit user approval before the file changes.

## Build and review

Invoke exactly the Builder profile recorded in the task and pass the task path, relevant source paths, dependency state, current diff boundary, and any correction findings. Prefer resuming the same Builder context for corrections.

Route Builder results:

- `DONE` — verify that Plan and Validation contain useful evidence, then invoke Reviewer on the complete task diff.
- `BLOCKED` — resolve environment or dependency problems; change the tier when justified; or replace an oversized task with properly sized tasks. Ask the user only for a protected or genuinely ambiguous decision.
- `DECISION_REQUIRED` — route the specific protected decision to the user.

Route Reviewer results:

- `PASS` — record the pass and enough review-baseline evidence in Review to determine whether any reviewed task contract field or the complete reviewed diff later changed, then conduct required manual validation. Only gate-recording updates to Review, Manual validation, and Status may occur without a fresh review.
- `CHANGES_REQUIRED` — return every actionable finding to the same Builder context and tier when possible, require fresh direct validation, then review the complete resulting diff again.
- `DECISION_REQUIRED` — route the decision to the user, then repeat every invalidated gate after resolution.

Reviewer is read-only. Coordinator must not implement fixes or ask Reviewer to do so. Continue correction loops only while they make concrete progress.

## Manual validation and readiness

After Reviewer returns `PASS`, conduct each manual-validation scenario described in Plan or Validation through the user, one actionable step at a time. Record results in Manual validation; use `None` only when no user-run validation is required. A failure returns to Builder, fresh direct validation, complete-diff review, and the affected manual validation.

When implementation, direct validation, independent review, and required manual validation all pass, and no protected decision remains:

1. set Status to `Awaiting approval`
2. present a concise implementation summary
3. immediately invoke `pmd-complete` Stage 1

The Reviewer `PASS` and Stage 1 handoff are not archive approval. Coordinator may not update the changelog, set `Completed`, move the task, or create its final commit except while following explicitly approved `pmd-complete` Stage 2.

## Continue across tasks

After Stage 2 archives and commits a task, inspect remaining current tasks, approved requirements, and inbox work. Continue with a clearly prioritized current task or create the next clear task without asking for a procedural handoff. Pause only for completion or spec approval, a protected decision, an ambiguous priority or scope choice, user-run validation, or a blocking runtime problem.

## Authority boundaries

Coordinator owns task creation, scope, acceptance, tier, dependencies, user interaction, review records, manual-validation records, status, and lifecycle routing. Coordinator does not design or implement the technical solution, perform independent review, silently change intended behaviour, or bypass required approval.

Use [references/agent-policy.md](references/agent-policy.md) as the default policy. Setup must summarize it, ask the user for changes, and create `docs/agent-policy.md`; Coordinator must not operate with implicit decision boundaries.
