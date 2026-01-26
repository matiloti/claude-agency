# Revision Workflow

> Owner: Orchestrator | Version: 1.0 | Last Updated: 2026-01-26

Execute the Revision Flow when a Judge issues `NEEDS_REVISION` verdict. This skill handles the revision cycle with enforced iteration limits.

## Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| failed_artifact | Yes | Path to the work that needs revision |
| judge_feedback | Yes | Path to the Judge's review file with feedback |
| agent_type | Yes | Which agent should revise: `ideator`, `architect`, `backend`, `frontend`, `qa`, `infrastructure` |
| iteration | No | Current iteration number (1, 2, or 3). Default: 1 |

## Preconditions

- [ ] Judge has issued `NEEDS_REVISION` verdict
- [ ] Original agent output and files exist at `failed_artifact`
- [ ] Judge feedback is documented at `judge_feedback`
- [ ] Iteration count is 1, 2, or 3 (not exceeded)

## Exit States

- **Success**: Judge issues `APPROVED` verdict
- **Failure**: 3 iterations exhausted without approval; escalated to user
- **Partial**: Dispute raised; awaiting Architect interpretation

---

## Hard Iteration Limit

**CRITICAL: Maximum 3 iterations. This limit is non-negotiable.**

After 3 failed revision attempts, the skill MUST halt and escalate to the user. Do NOT:
- Attempt a 4th revision
- Reduce scope to force approval
- Skip escalation

---

## Revision State Tracking

Track revision state in a structured format:

```markdown
## Revision State: {feature/task name}

**Original Artifact**: {path to original work}
**Judge Feedback**: {path to review file}
**Agent**: {agent type}

### Iteration History

| # | Date | Changes Made | Judge Verdict | Notes |
|---|------|--------------|---------------|-------|
| 1 | {date} | {summary of changes} | NEEDS_REVISION | {key feedback} |
| 2 | {date} | {summary of changes} | NEEDS_REVISION | {key feedback} |
| 3 | {date} | {summary of changes} | {verdict} | {outcome} |

### Current Status

- **Current Iteration**: {1, 2, or 3}
- **Status**: in_progress / approved / escalated / disputed
- **Semantic Diff**: {brief comparison of what changed between iterations}
```

---

## Workflow Steps

### Step 0: Validate Iteration Count

Check current iteration number:

```
if iteration > 3:
    HALT
    Trigger Escalation Protocol
    return FAILURE
```

### Step 1: Construct Revision Prompt

Build the revision prompt including:
1. Original task context
2. What was attempted
3. Judge's specific feedback (verbatim from review file)
4. Previous revision attempts (if any)
5. Instruction to create fix commits (not amend)

Reference: `prompt-templates/README.md` for the Revision Prompt Template.

### Step 2: Spawn Revision Agent

Spawn a new subagent with:
- The revision prompt from Step 1
- Instruction to address ALL feedback points
- Reminder to commit locally after changes

### Step 3: Agent Revises Work

Agent:
1. Reads Judge feedback
2. Makes targeted changes
3. Creates fix commits (preserves history)
4. Reports completion with summary of changes

### Step 4: Re-submit to Judge

Spawn Judge to re-review:
- Updated artifact
- Summary of changes made
- Previous feedback for comparison

### Step 5: Handle Verdict

| Verdict | Action |
|---------|--------|
| `APPROVED` | Return SUCCESS, proceed with workflow |
| `NEEDS_REVISION` | Increment iteration, return to Step 0 |
| `REJECTED` | Return FAILURE, escalate to user |
| `ROLLBACK_TO_PHASE_{N}` | Halt revision, trigger rollback flow |

---

## Interpretation Dispute Protocol

If the agent believes the Judge's feedback misinterprets requirements:

1. **Agent flags dispute** in completion summary:
   ```
   DISPUTE: [specific issue with Judge's interpretation]
   Evidence: [reference to spec/architecture that supports agent's view]
   ```

2. **Skill pauses** the revision cycle

3. **Orchestrator spawns Architect** with:
   - Original spec
   - Judge feedback
   - Agent's dispute
   - Request for authoritative interpretation

4. **Architect rules**:
   - If spec is ambiguous: Architect clarifies, updates docs
   - If Judge is correct: Agent proceeds with Judge's interpretation
   - If Agent is correct: Judge feedback is overridden, revision continues

5. **Resume** with clarified requirements

---

## Escalation Protocol (After 3 Failures)

When 3 revision attempts fail without approval:

### 1. Generate Escalation Summary

```markdown
## Escalation: {task name} - Revision Limit Reached

**Iterations Attempted**: 3
**Agent**: {agent type}
**Original Task**: {brief description}

### Revision History

| # | Changes Made | Judge Feedback |
|---|--------------|----------------|
| 1 | {changes} | {feedback summary} |
| 2 | {changes} | {feedback summary} |
| 3 | {changes} | {feedback summary} |

### Pattern Analysis

{What keeps failing? Is there a recurring issue?}

### Options for User

1. **Accept as-is**: Deploy with documented limitations
2. **Provide guidance**: Specific direction for the agent to follow
3. **Change approach**: Trigger architecture amendment
4. **Defer**: Move task to later phase

### Recommendation

{Orchestrator's recommendation based on pattern analysis}
```

### 2. Wait for User Decision

Do NOT proceed until user provides direction.

### 3. Resume with User Guidance

After user decides:
- If new approach: Reset iteration counter, start fresh
- If guidance provided: Spawn agent with user's instructions
- If accepted as-is: Document limitations, proceed
- If deferred: Update project status, move on

---

## Output Location

Revision tracking is maintained in:
- `knowledge/workflow-state.md` (iteration count, status)
- Session logs (detailed revision history)
- `knowledge/reviews/{agent-name}/` (Judge feedback files)

---

## Related Documentation

- Full workflow documentation: `knowledge/orchestration/workflows/revision-flow.md`
- Interpretation Dispute Protocol: `knowledge/orchestration/workflows/revision-flow.md`
- Workflow-skill mapping: `knowledge/orchestration/workflow-skill-mapping.md`
- Prompt templates: `prompt-templates/README.md`
