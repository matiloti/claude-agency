# Quick Fix Workflow

> Owner: Orchestrator | Version: 1.0 | Last Updated: 2026-01-26

Execute the Quick Fix Flow for small, isolated changes that don't require the full feature pipeline.

## Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| change_description | Yes | Description of the change to make |
| target_files | Yes | Files that will be modified (1-3 max) |
| agent_type | Yes | Which agent should implement: `infrastructure`, `backend`, `frontend` |

## Preconditions

- [ ] Change has been classified as Trivial tier (see eligibility criteria below)
- [ ] All 6 eligibility criteria are confirmed true
- [ ] No active blockers affecting the change

## Exit States

- **Success**: Judge APPROVED, code pushed to remote
- **Failure**: Judge REJECTED or revision complexity exceeds Quick Fix scope (convert to Standard Flow)
- **Partial**: N/A (Quick Fix is atomic)

---

## Eligibility Criteria

**ALL must be true** for a change to qualify as Quick Fix:

1. **Isolated change**: Affects 1-3 files maximum
2. **No API contract changes**: No new/modified endpoints or response shapes
3. **No new user-facing behavior**: No new features or UX changes
4. **Well-understood scope**: No investigation or design needed
5. **Low risk**: Failure won't cascade to other components
6. **No database schema changes**: No migrations required

If ANY criterion is false, use Standard Feature Flow instead.

---

## Workflow Steps

### Step 0: Validate Eligibility

Before proceeding, log the eligibility check:

```markdown
## Quick Fix Eligibility Check

**Change**: {change_description}
**Target Files**: {target_files}

| Criterion | Status | Evidence |
|-----------|--------|----------|
| Isolated change (1-3 files) | [x] | {e.g., "1 file: Dockerfile"} |
| No API contract changes | [x] | {confirmation} |
| No new user-facing behavior | [x] | {confirmation} |
| Well-understood scope | [x] | {confirmation} |
| Low risk | [x] | {confirmation} |
| No schema changes | [x] | {confirmation} |

**Validated by**: {orchestrator/user}
**Result**: ELIGIBLE / NOT ELIGIBLE
```

If NOT ELIGIBLE, stop and use Standard Feature Flow.

### Step 1: Identify Agent

Based on the change type:

| Change Type | Agent |
|-------------|-------|
| Dockerfile, docker-compose, CI/CD, env vars | Infrastructure Engineer |
| Backend code, Spring Boot config, Kotlin files | Backend Developer |
| Frontend code, React Native, Tailwind config | Frontend Developer |

### Step 2: Delegate

Spawn a subagent with:
- The appropriate prompt template for the agent
- The change description
- List of target files
- Instruction to commit locally after implementation

### Step 3: Review

Spawn Judge to review the change.

**Judge Verdict Handling**:

| Verdict | Action |
|---------|--------|
| `APPROVED` | Proceed to Step 4 |
| `NEEDS_REVISION` | If simple fix, allow 1 revision attempt. If complex, convert to Standard Flow |
| `REJECTED` | Log failure, escalate to user |

### Step 4: Push

If APPROVED:
1. Instruct agent to push committed changes to remote
2. Update `knowledge/project-status.md` if applicable
3. Log completion

---

## Output Location

No dedicated output folder. Quick Fixes are tracked via:
- Git commit history
- `knowledge/project-status.md` (if relevant)
- Session logs (both orchestrator and implementing agent)

---

## Use Cases

Quick Fix is appropriate for:
- Dockerfile fixes (port changes, base image updates)
- Config tweaks (application.yml, package.json settings)
- Dependency updates (version bumps)
- Typo corrections (in code comments, strings)
- CI/CD changes (workflow files, build scripts)
- Environment variable updates (docker-compose.yml)
- Small bug fixes (single-line or simple logic fixes)

Quick Fix is NOT appropriate for:
- New features (use Standard Feature Flow)
- New API endpoints (use Standard Feature Flow)
- Database schema changes (use Standard Feature Flow)
- Multi-file refactoring (use Standard Feature Flow)
- Changes requiring design decisions (use Standard Feature Flow)

---

## Related Documentation

- Full workflow documentation: `system/orchestration/workflows/quick-fix-flow.md`
- Workflow-skill mapping: `system/orchestration/workflow-skill-mapping.md`
