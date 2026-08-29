# PMD Agent Policy

This policy defines project-specific decision boundaries for PMD's coordinated workflow. Every PMD project requires it. Adapt the rules to the repository; PMD's mandatory spec-change and iteration-completion approvals still apply and cannot be removed by this policy.

## Require user decision

- Change intended product behaviour.
- Expand or remove iteration scope.
- Choose a meaningful architecture tradeoff with lasting maintenance consequences.
- Change public API compatibility.
- Change a persistent data model or migration strategy.
- Make an authentication, authorization, privacy, or security decision.
- Perform a destructive or difficult-to-reverse operation.
- Introduce a paid external service or a new material operating cost.

## Planner may decide

- Implementation architecture within approved behaviour and scope.
- Module boundaries and internal abstractions.
- Task decomposition and execution grouping.
- Execution-group dependencies and Worker-profile assignment.
- Automated test strategy and planned manual validation.

## Worker may decide

- Local implementation details inside the assigned execution group.
- Names of internal functions, types, and variables.
- Straightforward refactors required by the assigned implementation.
- Exact test cases needed to verify already-approved behaviour.

Worker and Reviewer return any unclear or protected decision to Coordinator. Coordinator routes it to Planner or the user according to this policy and PMD's mandatory approval rules.
