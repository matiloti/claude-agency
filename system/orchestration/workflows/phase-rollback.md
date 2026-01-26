# Workflow Phase Rollback

<!-- Last Updated: 2026-01-26 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

When the Judge determines a fundamental flaw exists in an earlier phase, this flow handles rolling back to that phase.

---

## 1. Judge Issues Verdict

Judge issues `ROLLBACK_TO_PHASE_{N}` verdict, specifying:
- Which phase needs to be redone (e.g., Architecture, Ideation).
- What the fundamental flaw is.
- What constraints the redo must address.

---

## 2. Orchestrator Actions

- Archives current artifacts by adding a `_superseded` suffix to affected knowledge files.
- Notifies the user of the rollback with rationale.
- Restarts the workflow from the specified phase with the Judge's constraints included in the prompt.
- All subsequent phases must be re-executed after the rolled-back phase.

---

## 3. Constraints

- Rollbacks to Ideation (phase 1) require user confirmation.
- Rollbacks to Architecture (phase 3) are allowed without user confirmation if the Judge provides clear rationale.
- Already-pushed code must be handled via new commits (not force-push).

---

## Related Documentation

- [Workflow Index](./README.md)
- [Standard Feature Flow](./standard-feature-flow.md)
- Judge Verdict Reference: [README.md](./README.md#judge-verdict-reference)
