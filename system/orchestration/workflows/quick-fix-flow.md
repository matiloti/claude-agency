# Quick Fix Flow

<!-- Last Updated: 2026-01-26 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

For small config/infrastructure/code changes that don't require the full feature pipeline.

---

## Preconditions

- [ ] Change has been classified as Trivial tier
- [ ] All eligibility criteria verified (see Decision Criteria below)

---

## Decision Criteria: Quick Fix vs Standard Flow

Use this checklist to determine if a change qualifies as a Quick Fix. ALL must be true:
- [ ] Isolated change (affects 1-3 files max)
- [ ] No API contract changes (no new/modified endpoints or response shapes)
- [ ] No new user-facing behavior (no new features or UX changes)
- [ ] Well-understood scope (no investigation or design needed)
- [ ] Low risk (failure won't cascade to other components)
- [ ] No database schema changes

If ANY checkbox is false, use Standard Feature Flow instead.

---

## Steps

### Step 0: Validate Eligibility

Log which criteria were checked:

```
Quick Fix Eligibility Check:
- [x] Isolated change: {description, e.g., "1 file: Dockerfile"}
- [x] No API changes: {confirmation}
- [x] No UX changes: {confirmation}
- [x] Well-understood: {confirmation}
- [x] Low risk: {confirmation}
- [x] No schema changes: {confirmation}
Validated by: {orchestrator/user}
```

### Step 1: Identify Agent

Determine the appropriate agent (Infrastructure Engineer, Frontend Developer, Backend Developer).

### Step 2: Delegate

Spawn a subagent with the relevant prompt template and the fix request.

### Step 3: Review

Judge reviews the change.

### Step 4: Done

Push if approved.

---

## Exit States

- **Success**: Judge APPROVED, code pushed
- **Failure**: Judge REJECTED or NEEDS_REVISION escalates beyond expectations (convert to Standard Flow)
- **Partial**: N/A (Quick Fix is atomic)

---

## Use Cases

Use this flow for:
- Dockerfile fixes
- Config tweaks
- Dependency updates
- Typo corrections
- CI/CD changes
- Environment variable updates
- Other small targeted changes

---

## Related Documentation

- [Workflow Index](./README.md)
- `/quick-fix` Skill: `.claude/skills/quick-fix/SKILL.md`
- Workflow-skill mapping: `../workflow-skill-mapping.md`
