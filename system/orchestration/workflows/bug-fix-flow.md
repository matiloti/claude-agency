# Bug Fix Flow

<!-- Last Updated: 2026-02-22 -->
<!-- Status: Current -->
<!-- Author: workflow-expert -->

For diagnosing and fixing bugs that require investigation but not full feature design.

---

## Preconditions

Before starting Bug Fix Flow:
- [ ] Bug has been reported with a description of observed vs expected behavior
- [ ] Bug has been classified as Standard tier (not Trivial - see Decision Criteria below)
- [ ] No active blockers in `knowledge/blockers/` preventing access to the affected system
- [ ] Affected component (backend or frontend) has been identified at a coarse level

---

## Decision Criteria: Bug Fix Flow vs Quick Fix vs Standard Feature Flow

Use Bug Fix Flow when ALL of the following are true:
- [ ] The root cause is not immediately obvious (investigation required)
- [ ] The fix does not introduce new behavior or new user-facing features
- [ ] No new API endpoints, schema changes, or infrastructure changes are required
- [ ] The fix is contained to existing code (backend, frontend, or both)

| Scenario | Use This Flow |
|----------|--------------|
| Root cause is obvious, 1-3 files affected, low risk | [Quick Fix Flow](./quick-fix-flow.md) |
| Root cause requires investigation, fix is corrective | **Bug Fix Flow** (this file) |
| Fix requires new feature, endpoint, or schema change | [Standard Feature Flow](./standard-feature-flow.md) |

---

## Steps

### Step 1: Reproduce

**CI Agent** attempts to reproduce the bug in a clean environment.

- Run the failing test case or manually trigger the bug scenario
- Capture the exact error output, stack trace, or incorrect behavior
- Produce a **Reproduction Report** confirming:
  - Steps to reproduce
  - Environment (branch, runtime, config)
  - Observed output vs expected output

If the bug cannot be reproduced, stop and escalate to user with the reproduction attempt log.

### Step 2: Root Cause Analysis

**Backend Developer** or **Frontend Developer** (based on bug location) investigates root cause.

- Trace the execution path from the symptom back to the origin
- Identify the exact line(s) of code or configuration responsible
- Document findings in a **Root Cause Analysis (RCA)**:
  ```
  Bug: {brief description}
  Root Cause: {exact cause}
  Affected File(s): {list}
  Why It Happened: {explanation}
  Fix Approach: {proposed fix}
  Regression Risk: {low/medium/high + rationale}
  ```

### Step 3: Review

**Judge** reviews the Root Cause Analysis and proposed fix approach.

Verdicts:
- `APPROVED` - Proceed to Step 4 (implementation)
- `NEEDS_REVISION` - Trigger [Revision Flow](./revision-flow.md) (max 3 iterations)
- `REJECTED` - RCA or approach is fundamentally wrong; escalate to user
- `ROLLBACK_TO_PHASE_{N}` - Trigger [Phase Rollback](./phase-rollback.md)

### Step 4: Implement Fix

**Backend Developer** or **Frontend Developer** (same agent as Step 2) implements the fix.

- Apply the minimal change necessary to correct the bug
- Write or update unit tests that directly cover the fixed behavior
- Include a regression test that would have caught this bug originally
- Commit with message format: `fix: {brief description of what was fixed}`

### Step 5: Review

**Judge** reviews the implementation.

Verdicts:
- `APPROVED` - Proceed to Step 6 (regression testing)
- `NEEDS_REVISION` - Trigger [Revision Flow](./revision-flow.md) (max 3 iterations)
- `REJECTED` - Escalate to user
- `ROLLBACK_TO_PHASE_{N}` - Trigger [Phase Rollback](./phase-rollback.md)

### Step 6: Regression Testing

**QA Tester** validates the fix and verifies no regressions.

- Confirm the original bug scenario no longer reproduces
- Run the full existing test suite (`./gradlew test` and/or `npm test`)
- Verify all previously passing tests still pass
- Document results in a **Regression Report**:
  ```
  Bug Scenario: RESOLVED / NOT RESOLVED
  Test Suite: {N} passing, {0} failing
  New Tests Added: {N}
  Regressions Introduced: none / {list}
  ```

### Step 7: Final Review

**Judge** reviews QA Tester's Regression Report and gives final verdict.

Verdicts:
- `APPROVED` - Bug fix complete; push to remote
- `NEEDS_REVISION` - Regressions found; return to Step 4 with findings
- `REJECTED` - Fix caused unacceptable side effects; escalate to user

---

## Exit States

- **Success**: All steps completed, final Judge verdict is `APPROVED`, fix pushed to remote
- **Failure**: Judge issues `REJECTED` at any step, or bug cannot be reproduced, or 3 revision iterations exhausted
- **Partial**: Fix is correct but regressions block completion; partial work committed locally, blocker documented in `knowledge/blockers/`

---

## Related Documentation

- [Workflow Index](./README.md)
- [Quick Fix Flow](./quick-fix-flow.md) - use instead if root cause is obvious and scope is trivial
- [Standard Feature Flow](./standard-feature-flow.md) - use instead if fix requires new features or schema changes
- [Revision Flow](./revision-flow.md)
- [Phase Rollback](./phase-rollback.md)
