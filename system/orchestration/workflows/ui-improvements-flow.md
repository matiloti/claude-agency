# UI Improvements Flow

<!-- Last Updated: 2026-02-22 -->
<!-- Status: Current -->
<!-- Author: workflow-expert -->

For purely visual and styling changes: layout refinements, responsive design fixes, accessibility improvements, CSS/styling updates.

---

## Preconditions

Before starting UI Improvements Flow:
- [ ] Change has been confirmed as frontend-only (no backend or API changes required)
- [ ] Affected screens or components have been identified
- [ ] No active blockers in `knowledge/blockers/` affecting the frontend build
- [ ] Target viewports for verification have been specified (default: 375px, 768px, 1280px)

---

## Decision Criteria: UI Improvements Flow vs Standard Feature Flow

Use UI Improvements Flow when ALL of the following are true:
- [ ] Change is limited to visual appearance, layout, or styling
- [ ] No new components that alter user workflows or introduce new functionality
- [ ] No backend changes (no new API calls, no data model changes)
- [ ] No new routing or navigation changes

| Scenario | Use This Flow |
|----------|--------------|
| CSS fix, color/spacing adjustment, font change | **UI Improvements Flow** (this file) |
| Responsive layout fix with no new functionality | **UI Improvements Flow** (this file) |
| Accessibility fix (ARIA labels, contrast, focus) | **UI Improvements Flow** (this file) |
| New UI component that requires new API data | [Standard Feature Flow](./standard-feature-flow.md) |
| UI change that requires backend changes | [Standard Feature Flow](./standard-feature-flow.md) |
| Trivial one-line CSS typo or single-file fix | [Quick Fix Flow](./quick-fix-flow.md) |

---

## Steps

### Step 1: Visual Audit

**Frontend Developer** conducts a visual audit of the current state.

- Document the current visual state with screenshots or descriptions for each affected screen/component
- Identify all specific issues: layout breaks, spacing inconsistencies, accessibility failures, responsive problems
- List each affected file
- Produce a **Visual Audit Report**:
  ```
  Affected Screens/Components: {list}
  Issues Found:
    - {screen/component}: {description of issue}
  Viewports to Test: 375px (mobile), 768px (tablet), 1280px (desktop)
  Accessibility Concerns: {list or "none identified"}
  Files to Modify: {list}
  ```

### Step 2: Implement Changes

**Frontend Developer** implements the UI improvements.

- Apply styling, layout, and accessibility changes
- Capture screenshots or visual descriptions of the after state for each changed screen/component at all target viewports
- Verify changes do not break any existing component tests (`npm test`)
- Structure commit as: `style: {brief description of what was changed}`

### Step 3: Review

**Judge** reviews the implementation using before/after context.

The orchestrator MUST provide the Judge with:
- The Visual Audit Report from Step 1 (before state)
- The after-state screenshots or descriptions from Step 2
- The list of modified files

Verdicts:
- `APPROVED` - Proceed to Step 4 (QA testing)
- `NEEDS_REVISION` - Trigger [Revision Flow](./revision-flow.md) (max 3 iterations)
- `REJECTED` - Approach is fundamentally wrong; escalate to user
- `ROLLBACK_TO_PHASE_{N}` - Trigger [Phase Rollback](./phase-rollback.md)

### Step 4: Viewport and Accessibility Testing

**QA Tester** validates the changes across all required viewports and checks accessibility.

Viewport testing (required for every run):
- [ ] **375px** (mobile): Layout renders correctly, no overflow, touch targets adequate
- [ ] **768px** (tablet): Breakpoint transitions as intended
- [ ] **1280px** (desktop): Full layout correct, no unintended whitespace

Accessibility checks:
- [ ] Color contrast meets WCAG AA (4.5:1 for text, 3:1 for large text)
- [ ] Interactive elements are keyboard-navigable
- [ ] ARIA labels are present and correct on interactive elements
- [ ] Focus indicators are visible

Produce a **Viewport and Accessibility Report**:
```
Viewport Results:
  375px:  PASS / FAIL - {notes}
  768px:  PASS / FAIL - {notes}
  1280px: PASS / FAIL - {notes}

Accessibility Results:
  Contrast:     PASS / FAIL - {notes}
  Keyboard nav: PASS / FAIL - {notes}
  ARIA labels:  PASS / FAIL - {notes}
  Focus:        PASS / FAIL - {notes}

Existing Tests: {N} passing, {0} failing
```

### Step 5: Final Review

**Judge** reviews the Viewport and Accessibility Report and gives final verdict.

Verdicts:
- `APPROVED` - UI improvements complete; push to remote
- `NEEDS_REVISION` - Viewport or accessibility failures; return to Step 2 with findings
- `REJECTED` - Systemic issues require architectural rethink; escalate to user

---

## Exit States

- **Success**: All steps completed, final Judge verdict is `APPROVED`, changes pushed to remote
- **Failure**: Judge issues `REJECTED` at any step, or viewport/accessibility failures cannot be resolved within 3 revision iterations
- **Partial**: Some screens pass but others remain blocked; committed work documented, blocker filed in `knowledge/blockers/`

---

## Related Documentation

- [Workflow Index](./README.md)
- [Quick Fix Flow](./quick-fix-flow.md) - use instead for single-file trivial styling fixes
- [Standard Feature Flow](./standard-feature-flow.md) - use instead if backend changes are required
- [Revision Flow](./revision-flow.md)
- [Phase Rollback](./phase-rollback.md)
