# Architecture Amendment Pipeline

<!-- Last Updated: 2026-01-27 -->
<!-- Status: Current -->

Handles architectural amendments when implementation reveals design flaws.

---

## Flaw Classification

Before triggering an amendment, classify the issue:

| Classification | Criteria | Action |
|----------------|----------|--------|
| **Amendment Required** | API contract change, schema change, security boundary change | Proceed with this pipeline |
| **Clarification Only** | Missing detail that doesn't change contracts, ambiguous wording | Developer asks Architect directly; no amendment |
| **Developer Misunderstanding** | Spec covers the case but developer missed it | Developer re-reads docs; no amendment |

---

## Pipeline Steps

### Step 1: Document Issue

Developer documents the issue in their completion summary under "Blockers Encountered."

### Step 2: Spawn Architect

Orchestrator spawns Architect with:
- Original architecture files
- Developer's issue description
- Current implementation state

### Step 3: Produce Amendment

Architect produces amendment:
- Updated API spec, schema change, or other affected artifacts
- Rationale for the change
- Impact assessment (affected components/agents)

### Step 4: Judge Review

Judge reviews the amendment against:
- Consistency with existing architecture
- Technical feasibility
- Scope appropriateness

### Step 5: Handle Verdict

| Verdict | Action |
|---------|--------|
| `APPROVED` | Proceed to Step 6 |
| `NEEDS_REVISION` | Proceed to Step 5a |
| `REJECTED` | Proceed to Step 5b |

#### Step 5a: Revision Cycle

1. Judge provides specific feedback to Architect
2. Architect revises amendment (return to Step 3)
3. **Maximum 2 revision attempts**
4. If still not approved after 2 attempts: escalate to user

#### Step 5b: Rejection Handling

1. Judge documents rejection reason
2. Orchestrator escalates to user immediately
3. User decides:
   - Provide guidance for new approach
   - Override rejection
   - Abandon the feature/change

### Step 6: Notify and Resume

1. Orchestrator notifies affected agents with updated architecture context
2. Creates/updates `knowledge/amendments/{feature}-{date}.md`
3. Resumes blocked work

---

## Exit States

| State | Condition | Next Action |
|-------|-----------|-------------|
| **Success** | Amendment approved | Resume implementation with updated architecture |
| **Revision Exhausted** | 2 revision attempts failed | User provides guidance or abandons |
| **Rejected** | Fundamental approach rejected | User decides path forward |
| **User Override** | User overrides rejection | Resume with user-specified approach |

---

## Related Documentation

- [Pipeline Index](./README.md)
- [Parallel Work Protocol](../workflows/parallel-work-protocol.md)
- [Phase Rollback](../workflows/phase-rollback.md)
