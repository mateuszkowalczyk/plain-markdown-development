# OpenCode v2 MVP dogfood report

Date: 2026-08-29

The reference integration was exercised in an isolated Git repository with OpenCode 2 beta (`opencode2 v0.0.0-beta-18684`) as Coordinator. The test iteration implemented a small standard-library Python title-to-slug API and command-line interface, then completed the normal `pmd-complete` approval, changelog, and archive workflow.

## Coverage

- Two serial execution groups: a Python API group and a command-line group.
- Two configured Worker profiles: an OpenCode-native Worker and a synchronous external Codex CLI Worker.
- OpenCode v2 discovered the copied PMD skills directly from `.agents/skills/`; no compatibility copy or plugin was required.
- One replanning event that replaced an invalid packaging assumption while preserving task IDs, scope, behaviour, and the external Worker assignment.
- Direct automated validation, independent group reviews, two user-run manual CLI scenarios, and a final whole-iteration review.
- One controlled, uncommitted fault injection added a needless one-use helper. Reviewer returned `CHANGES_REQUIRED` with a concrete simplification suggestion; the assigned external Worker removed it, reran validation, and Reviewer returned `PASS`.
- Explicit completion approval followed by a test-repository changelog update and archive move.

The controlled fault was introduced only to exercise the required correction branch after the natural implementation received `PASS`. It was never committed and did not alter the intended product behaviour.

## Observations

- **Coordinator reasoning:** manageable for a small serial iteration. Markdown plus Git was sufficient durable state; no database, daemon, queue, background session, or custom orchestration tool was needed.
- **Planning:** Planner produced useful dependency-ordered groups and corrected the stale packaging premise without changing approved behaviour. The initial integration prompt did not enforce a separate post-replan checkpoint strongly enough, so the first group checkpoint also captured the plan revision.
- **Worker selection:** the native profile was sensible for the small API group; the external profile successfully handled the CLI group and a focused correction pass. Both profiles coexisted in one working tree.
- **Worker context:** iteration path, task and group IDs, sources, acceptance criteria, manual-validation plan, and review findings were sufficient. The external CLI returned the required semantic Worker result.
- **Review boundaries:** Reviewer did not duplicate implementation or the Worker's direct validation role. Its read-only policy meant that stale execution evidence had to be returned to Worker for a fresh test run, which preserved role separation.
- **Simplification:** the controlled one-use-helper finding was specific, behaviour-preserving, and useful. Re-reviewing the complete corrected diff worked as intended.
- **Manual validation:** stepwise interaction was understandable for visible output. Verifying exit status and the stdout/stderr split required an explicit capture command; the integration guide now recommends providing one.
- **Checkpoints:** group commits provided adequate review boundaries. A distinct planning/replanning checkpoint is necessary before implementation begins; the reference prompt and runtime example now say so explicitly.
- **Working-tree hygiene:** Python validation created untracked `__pycache__` files. Generated artifacts must be ignored or cleaned before checkpoint and final-readiness checks.

## Result

The full workflow completed successfully: replan → native Worker → Reviewer → checkpoint → external Worker → Reviewer correction cycle → manual validation → checkpoint → whole-iteration review → `Awaiting approval` → explicit completion approval → changelog and archive.

Dogfooding found documentation and prompt-level refinements, but no evidence that PMD needs additional runtime infrastructure.
