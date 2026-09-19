# PMD Agent Policy

This policy defines project-specific decision boundaries for PMD's coordinated workflow. Every PMD project requires it. Adapt the rules to the repository; PMD's mandatory spec-change and task-completion approvals cannot be removed.

## Require user decision

- Change intended product behaviour.
- Materially expand or remove task scope after work begins.
- Choose a meaningful architecture tradeoff with lasting maintenance consequences.
- Change public API compatibility.
- Change a persistent data model or migration strategy.
- Make an authentication, authorization, privacy, or security decision.
- Perform a destructive or difficult-to-reverse operation.
- Introduce a paid external service or a new material operating cost.

## Coordinator may decide

- Create and prioritize a task from clear user requests and approved sources.
- Define task outcome, acceptance, sources, and dependencies without changing intended behaviour.
- Size a task for one Builder context and select its `simple` or `complex` tier.
- Split work into independently deliverable tasks when approved scope and behaviour remain unchanged.
- Route review findings and plan user-run manual validation.

## Builder may decide

- Technical implementation architecture within approved behaviour and task scope.
- Module boundaries and internal abstractions.
- Local implementation details and names.
- Straightforward refactors required by the task.
- Automated test strategy, exact test cases, and direct-validation procedure.

Builder and Reviewer return unclear or protected decisions to Coordinator. Only Coordinator normally interacts with the user.
