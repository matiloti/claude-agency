# Feature Improvement Flow

<!-- Last Updated: 2026-02-22 -->
<!-- Status: Current -->
<!-- Author: workflow-expert -->

For enhancing existing features without a full rebuild. Skips ideation; uses a scoped architecture phase instead of full design.

---

## Preconditions

Before starting Feature Improvement Flow:
- [ ] The existing feature to be improved has been identified by name/area
- [ ] Work has been classified as Standard tier and confirmed as an enhancement (not a new feature, not a bug fix)
- [ ] No active blockers in `knowledge/blockers/` affecting the target feature
- [ ] User has confirmed the improvement scope (what changes, what stays the same)

---

## Decision Criteria: Feature Improvement Flow vs Other Flows

Use Feature Improvement Flow when ALL of the following are true:
- [ ] The feature already exists and is working; this change makes it better
- [ ] The improvement requires analysis of existing behavior before implementation
- [ ] No entirely new features, screens, or endpoints are being added from scratch

| Scenario | Use This Flow |
|----------|--------------|
| Adding pagination to an existing list screen | **Feature Improvement Flow** (this file) |
| Improving error handling in an existing API endpoint | **Feature Improvement Flow** (this file) |
| Optimizing an existing query or algorithm | **Feature Improvement Flow** (this file) |
| Adding a filter option to an existing search | **Feature Improvement Flow** (this file) |
| Building a new feature that does not exist yet | [Standard Feature Flow](./standard-feature-flow.md) |
| Fixing incorrect behavior (a bug) | [Bug Fix Flow](./bug-fix-flow.md) |
| Trivial change with obvious scope | [Quick Fix Flow](./quick-fix-flow.md) |

---

## Steps

### Step 1: Current State Analysis and Scope Definition

**Architect** analyzes the existing feature and defines the improvement scope.

This is a focused scoping session, not a full architectural design. The Architect produces an **Improvement Scope Document**:
```
Feature Being Improved: {name}
Current Behavior: {description}
Desired Behavior After Improvement: {description}
Components Affected:
  Backend: {list of services/files/endpoints, or "none"}
  Frontend: {list of components/screens/files, or "none"}
Approach:
  {concise description of what will change and how}
Parallel Work Eligible: yes / no
  (yes = both backend and frontend changes are independent enough to parallelize)
Existing Tests to Preserve: {list or "run full suite"}
New Test Coverage Required: {description}
Out of Scope: {explicit list of what this improvement does NOT touch}
```

### Step 2: Review

**Judge** reviews the Improvement Scope Document.

Verdicts:
- `APPROVED` - Proceed to implementation (Steps 3-6)
- `NEEDS_REVISION` - Trigger [Revision Flow](./revision-flow.md) (max 3 iterations)
- `REJECTED` - Scope is unclear or approach is fundamentally wrong; escalate to user
- `ROLLBACK_TO_PHASE_{N}` - Trigger [Phase Rollback](./phase-rollback.md)

---

### Implementation Phase

After scope is approved, implementation proceeds based on which components are affected.

**If backend-only**: Execute Steps 3-4, then skip to Step 7.
**If frontend-only**: Skip Steps 3-4, execute Steps 5-6, then proceed to Step 7.
**If both backend and frontend**: Steps 3-4 and Steps 5-6 may run in parallel (see [Parallel Work Protocol](./parallel-work-protocol.md)), then proceed to Step 7.

---

### Step 3: Backend Implementation (if backend changes required)

**Backend Developer** implements the backend portion of the improvement.

- Implement changes consistent with the Improvement Scope Document
- Maintain backward compatibility with existing consumers unless scope explicitly permits breaking changes
- Update or extend existing tests; add new tests for the improved behavior
- Commit with message: `feat: improve {feature name} - {brief description}`

### Step 4: Backend Review (if Step 3 executed)

**Judge** reviews the backend implementation.

Verdicts:
- `APPROVED` - Backend complete; proceed (or await frontend if running in parallel)
- `NEEDS_REVISION` - Trigger [Revision Flow](./revision-flow.md) (max 3 iterations)
- `REJECTED` - Escalate to user
- `ROLLBACK_TO_PHASE_{N}` - Trigger [Phase Rollback](./phase-rollback.md)

### Step 5: Frontend Implementation (if frontend changes required)

**Frontend Developer** implements the frontend portion of the improvement.

- Implement UI changes consistent with the Improvement Scope Document
- Ensure existing component behavior is preserved where not in scope
- Update or extend existing frontend tests; add new tests for improved behavior
- Commit with message: `feat: improve {feature name} - {brief description}`

### Step 6: Frontend Review (if Step 5 executed)

**Judge** reviews the frontend implementation.

Verdicts:
- `APPROVED` - Frontend complete; proceed to Step 7
- `NEEDS_REVISION` - Trigger [Revision Flow](./revision-flow.md) (max 3 iterations)
- `REJECTED` - Escalate to user
- `ROLLBACK_TO_PHASE_{N}` - Trigger [Phase Rollback](./phase-rollback.md)

---

### Step 7: Testing

**QA Tester** validates both existing behavior and the new improved behavior.

Testing must cover two categories:

**Existing Behavior (Regression)**:
- Run the full test suite (`./gradlew test` and/or `npm test`)
- Verify all previously passing tests still pass
- Confirm out-of-scope areas are unaffected

**Improved Behavior (New Coverage)**:
- Validate the improved behavior matches the Improvement Scope Document
- Test edge cases introduced by the improvement
- Confirm new tests added in Steps 3 and/or 5 pass

Produce a **Test Report**:
```
Existing Behavior: {N} tests passing, {0} regressions
Improved Behavior: {description of validation performed}
  Result: VERIFIED / PARTIAL / FAILED
Edge Cases Tested: {list}
New Tests: {N} added
```

### Step 8: Final Review

**Judge** reviews the Test Report and gives final verdict.

Verdicts:
- `APPROVED` - Feature improvement complete; push to remote
- `NEEDS_REVISION` - Issues found; return to the relevant implementation step with findings
- `REJECTED` - Fundamental problems with the improvement; escalate to user

---

## Exit States

- **Success**: All applicable steps completed, final Judge verdict is `APPROVED`, code pushed to remote
- **Failure**: Judge issues `REJECTED` at any step, or 3 revision iterations exhausted without resolution
- **Partial**: One track (backend or frontend) approved but the other is blocked; approved work committed, blocker documented in `knowledge/blockers/`

---

## Related Documentation

- [Workflow Index](./README.md)
- [Standard Feature Flow](./standard-feature-flow.md) - use instead for net-new features
- [Bug Fix Flow](./bug-fix-flow.md) - use instead when fixing incorrect behavior
- [Quick Fix Flow](./quick-fix-flow.md) - use instead for trivial, well-understood changes
- [Parallel Work Protocol](./parallel-work-protocol.md)
- [Revision Flow](./revision-flow.md)
- [Phase Rollback](./phase-rollback.md)
