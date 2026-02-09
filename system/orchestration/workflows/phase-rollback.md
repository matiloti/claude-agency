# Phase Rollback Protocol

<!-- Last Updated: 2026-01-27 -->
<!-- Status: Current -->
<!-- Author: prompt-engineer -->

When the Judge identifies a fundamental flaw in an earlier phase, this protocol governs the rollback process.

---

## Trigger

Judge issues `ROLLBACK_TO_PHASE_{N}` verdict specifying:
- Target phase number and name
- Fundamental flaw description
- Constraints for redo

---

## Phase Confirmation Matrix

| Phase | Name | User Confirmation | Rationale |
|-------|------|-------------------|-----------|
| 1 | Ideation | Required | Fundamental direction change |
| 2 | Review | Not required | Judge-initiated, rationale provided |
| 3 | Architecture | Not required | Technical decision (clear rationale) |
| 4 | Infrastructure | Not required | Technical decision |
| 5+ | Implementation | Not required | Technical decision |

---

## Orchestrator Actions

1. Archive current artifacts: add `_superseded` suffix to affected knowledge files
2. Notify user of rollback with rationale
3. Restart workflow from specified phase with Judge's constraints in prompt
4. Re-execute all subsequent phases after rolled-back phase

---

## User Denial Handling (Phase 1 Only)

If user denies Phase 1 rollback request:

| Option | Description | When to Use |
|--------|-------------|-------------|
| Proceed with risks | Document risks in knowledge file, continue | Minor concerns, user accepts consequences |
| Escalate for discussion | Pause workflow, gather more context | Ambiguous situation, need clarification |
| Abandon feature | Archive all artifacts, close feature | Irreconcilable conflict, user rejects direction |

**Required**: User must explicitly choose one option. Do not proceed without explicit selection.

---

## Constraints

- Already-pushed code: handle via new commits, never force-push
- Archived files: preserve for audit trail, do not delete
- Subsequent phases: all must re-execute after rollback target

---

## Related Documentation

- [Workflow Index](./README.md)
- [Standard Feature Flow](./standard-feature-flow.md)
- Judge Verdict Reference: [README.md](./README.md#judge-verdict-reference)
