# Workflow Expert Analysis Report

> Analyst: workflow-expert
> Date: 2026-01-27
> Artifacts Analyzed: revision-flow.md, phase-rollback.md

---

# Process Analysis: Revision Flow

**Source**: `/Users/matias/Projects/claude-agency/system/orchestration/workflows/revision-flow.md`

## Classification

- **Current Name**: Revision Flow
- **Correct Classification**: **State Machine** (with workflow elements)
- **Verdict**: HYBRID
- **Recommendation**: Rename to "Revision State Machine" or keep as "Revision Flow" with explicit acknowledgment that it's a state machine wrapped in workflow documentation. The core logic is state-driven: iteration count determines state (attempt_1, attempt_2, attempt_3, escalated), and verdicts trigger transitions. The "flow" naming is acceptable given the system's conventions, but the documentation should formalize the state machine aspect.

### Classification Evidence

**State Machine characteristics present:**
- Finite states: attempt_1, attempt_2, attempt_3, approved, escalated, disputed
- Explicit transitions: NEEDS_REVISION increments attempt, APPROVED exits, 3 failures -> escalated
- Guards: iteration <= 3 check before proceeding
- Events: Judge verdicts trigger transitions

**Workflow characteristics present:**
- Multiple actors: Orchestrator, Developer, Judge, Architect (in dispute)
- Decision points: Dispute protocol introduces judgment-based branching

## Structure Summary

- **States/Phases**: 6 (implicit: attempt_1, attempt_2, attempt_3, approved, escalated, disputed)
- **Transitions**: 8 (NEEDS_REVISION x3, APPROVED x3, escalation, dispute)
- **Decision Points**: 3 (iteration check, verdict handling, dispute resolution)
- **Actors**: Orchestrator, Developer Agent, Judge, Architect (dispute only)

## Gap Analysis

### Missing States

- **timeout**: What happens if an agent takes too long to complete a revision? No timeout state is defined.
- **cancelled**: What if the user cancels the revision mid-cycle? There's no explicit cancellation path.
- **rollback_triggered**: The flow mentions ROLLBACK_TO_PHASE_{N} as a possible verdict but doesn't define this as an exit state (only mentioned in the skill, not the workflow doc).

### Missing Transitions

- **disputed -> escalated**: If Architect rules ambiguously or cannot resolve, what happens? The flow assumes Architect always resolves.
- **partial -> approved**: The Partial exit state exists but no transition leads from it to completion.
- **any_state -> cancelled**: No cancellation transitions defined.

### Undefined Decision Points

- **When to flag a dispute**: The protocol says "if a developer believes..." but provides no objective criteria. This is appropriate for judgment calls but could benefit from examples.
- **Rollback vs. Escalation**: If Judge issues ROLLBACK during revision, does that count as a failed iteration? The skill says "Halt revision, trigger rollback flow" but doesn't clarify iteration accounting.

## Redundancy Analysis

### Duplicate Logic

- **Revision Flow vs. Revision Skill**: The workflow file (`revision-flow.md`) and the skill file (`SKILL.md`) contain overlapping content. The skill is more detailed (state tracking format, step-by-step instructions) while the workflow is higher-level. This is intentional (skill implements workflow) but creates maintenance burden when updating.
- **Escalation Protocol**: Described in both files with slightly different emphasis. The workflow focuses on options; the skill includes a detailed markdown template.

### Unreachable Elements

- **None identified**: All states have entry paths.

## Optimization Opportunities

### Structural

1. **Formalize state machine**: Add an explicit state diagram to the workflow doc. The current text-based description works but a visual would prevent ambiguity.
   - Expected benefit: Clearer understanding, easier onboarding

2. **Extract Dispute Protocol**: The Interpretation Dispute Protocol is a sub-workflow within the revision flow. Consider extracting it as a separate skill or micro-workflow.
   - Expected benefit: Reusability if disputes occur in other contexts; cleaner separation

3. **Add timeout handling**: Define maximum time per revision attempt and transition to escalation if exceeded.
   - Expected benefit: Prevents indefinite blocking

### Naming

1. **"Partial" exit state -> "Disputed"**: The current name "Partial" is vague. Since it's specifically for dispute awaiting Architect, rename to "Disputed".
   - Reason: Clarity; "Partial" could mean many things

### Skill Candidacy

- **Suitable for skill?**: YES (already implemented)
- **Rationale**: The revision flow meets all skill criteria:
  - **Bounded scope**: Fixed steps (construct prompt -> spawn agent -> review -> handle verdict)
  - **Self-contained**: No mid-execution orchestrator intervention (except dispute)
  - **Deterministic**: Same iteration count + verdict = same behavior
  - **Atomic**: Completes with APPROVED, ESCALATED, or DISPUTED

**Observation**: The skill implementation (`SKILL.md`) is more complete than the workflow documentation. Consider the skill as the source of truth and the workflow as an overview/index entry.

## Recommendations

1. **Add explicit state diagram** to `revision-flow.md` showing: attempt_1 -> attempt_2 -> attempt_3 -> escalated, with APPROVED exits at each stage and DISPUTED as a pausing state.

2. **Define ROLLBACK iteration accounting**: Clarify whether a ROLLBACK verdict during revision counts toward the 3-iteration limit or resets it.

3. **Add timeout state and transition**: "If no response within X hours, escalate to user with status: revision_timeout"

4. **Add cancellation path**: "User can cancel revision at any point. Cancellation archives current state and marks task as deferred."

5. **Consolidate duplicate content**: Add a note to `revision-flow.md` stating "For implementation details, see `.claude/skills/revision/SKILL.md`" and remove duplicate procedural content from the workflow file. Keep the workflow focused on when/why; let the skill handle how.

---

# Process Analysis: Phase Rollback

**Source**: `/Users/matias/Projects/claude-agency/system/orchestration/workflows/phase-rollback.md`

## Classification

- **Current Name**: Workflow Phase Rollback
- **Correct Classification**: **Procedure/Protocol**
- **Verdict**: MISNAMED
- **Recommendation**: Rename to "Phase Rollback Protocol" or "Phase Rollback Procedure". This is not a workflow (no iteration, minimal branching) nor a state machine (no explicit states). It's a procedure: a linear sequence of steps triggered by a specific event. The word "Workflow" in the title creates false expectations of complexity.

### Classification Evidence

**Procedure characteristics present:**
- Triggered by single event: `ROLLBACK_TO_PHASE_{N}` verdict
- Linear execution: Archive -> Notify -> Restart
- No iteration or loops
- Minimal branching (only user confirmation gate for phase 1)

**NOT a workflow because:**
- No judgment-based decision points after initiation
- No potential for revisiting steps
- Minimal actor coordination (mostly orchestrator actions)

**NOT a state machine because:**
- No explicit states defined
- No state tracking during execution
- Single-pass execution

## Structure Summary

- **States/Phases**: 3 (numbered sections, but not formal states)
- **Transitions**: 2 (initiate -> archive -> restart, with optional user confirmation gate)
- **Decision Points**: 1 (phase 1 requires user confirmation)
- **Actors**: Judge (trigger), Orchestrator (execution), User (confirmation for phase 1)

## Gap Analysis

### Missing States

- **N/A**: This is a procedure, not a state machine. However, if converted to a state machine, it would need: triggered, awaiting_confirmation, archiving, restarting, completed.

### Missing Transitions

- **user_denies_rollback**: What happens if user confirmation is requested for phase 1 rollback and user says no? The document doesn't address denial.
- **archive_failure**: What if archiving fails (disk full, permission error)? No error handling defined.
- **restart_failure**: What if restarting the workflow fails? No recovery path.

### Undefined Decision Points

- **Which phases require confirmation**: Document says "phase 1 requires confirmation" and "phase 3 allowed without confirmation." What about phases 2, 4, 5, etc.? Only two phases are explicitly addressed.
- **"Clear rationale" definition**: Phase 3 rollback is allowed without confirmation "if the Judge provides clear rationale." What constitutes "clear"? This is subjective.
- **Code handling**: "Already-pushed code must be handled via new commits" - but who decides what commits to make? Is there a revert strategy?

## Redundancy Analysis

### Duplicate Logic

- **Verdict handling overlap with README.md**: The README.md Verdict Decision Tree mentions ROLLBACK_TO_PHASE_{N} and describes the same actions (archive, restart). This is appropriate cross-referencing, not problematic duplication.

### Unreachable Elements

- **None identified**: The procedure is simple enough that all steps are reachable.

## Optimization Opportunities

### Structural

1. **Define all phase confirmation requirements**: Create a table showing which phases require user confirmation and which don't. Don't leave phases 2, 4, 5+ ambiguous.
   - Expected benefit: Eliminates ambiguity when implementing

2. **Add error handling section**: Define what happens if archiving or restarting fails.
   - Expected benefit: Robustness in edge cases

3. **Define rollback denial path**: What happens if user says no to phase 1 rollback?
   - Expected benefit: Complete coverage of user interaction scenarios

### Naming

1. **"Workflow Phase Rollback" -> "Phase Rollback Protocol"**: Remove "Workflow" from the name to avoid misclassification.
   - Reason: This is a procedure/protocol, not a workflow. The current name suggests more complexity than exists.

### Skill Candidacy

- **Suitable for skill?**: BORDERLINE YES
- **Rationale**:
  - **Bounded scope**: Yes - fixed steps: archive, notify, restart
  - **Self-contained**: Mostly - user confirmation interrupts for phase 1
  - **Deterministic**: Yes - same phase + same rationale = same behavior
  - **Atomic**: No - user confirmation breaks atomicity; restart triggers a full workflow

**Recommendation**: Could be implemented as a skill with the caveat that it's an "initiating" skill (triggers another workflow) rather than a "completing" skill. The user confirmation requirement for phase 1 makes it non-atomic, but this could be handled by making the skill async (return "awaiting_confirmation" state).

## Recommendations

1. **Rename to "Phase Rollback Protocol"**: Remove "Workflow" from the name to accurately reflect its nature as a procedure.

2. **Add phase confirmation matrix**:
   ```
   | Phase | Name | User Confirmation Required |
   |-------|------|---------------------------|
   | 1 | Ideation | Yes |
   | 2 | Review | ? |
   | 3 | Architecture | No (if clear rationale) |
   | 4 | Infrastructure | ? |
   | 5 | Implementation | ? |
   ```

3. **Define rollback denial handling**: "If user denies phase 1 rollback, options: (a) proceed with current flawed approach and document risks, (b) escalate to deeper discussion, (c) abandon the feature."

4. **Add error handling**: "If archiving fails: [action]. If restart fails: [action]."

5. **Define code revert strategy**: "When rolling back after code has been pushed: (a) create revert commits for all changes since phase N, (b) do not force-push, (c) document revert rationale in commit messages."

6. **Consider skill implementation**: Create a `/rollback` skill that handles the procedural aspects, with clear async handling for user confirmation scenarios.

---

# Cross-Artifact Observations

## Relationship Between Artifacts

The Revision Flow and Phase Rollback are connected via the ROLLBACK_TO_PHASE_{N} verdict. When a Judge issues ROLLBACK during a revision cycle:

1. Revision Flow should halt (documented in skill, not in workflow)
2. Phase Rollback should trigger
3. After rollback completes, the entire workflow restarts from the target phase

**Gap**: Neither document explicitly describes the handoff. The revision skill says "Halt revision, trigger rollback flow" but doesn't specify whether revision state is preserved (for potential resume after rollback) or discarded.

## System-Wide Patterns

1. **Skill-Workflow Duality**: Both artifacts have (or could have) skill implementations. The pattern of "workflow doc for overview, skill for implementation" is emerging but not formalized. Consider adding a header to each workflow doc: "Implementation: [skill path] or [N/A - not skill-suitable]"

2. **User Confirmation Inconsistency**: Revision Flow escalates to user after 3 failures (async). Phase Rollback requires sync confirmation for phase 1. The interaction models differ but this isn't documented as intentional.

3. **Error Handling Gap**: Neither document addresses technical failures (timeouts, crashes, resource exhaustion). This is a system-wide gap worth addressing.

## Consolidation Opportunities

1. **Create a Verdict Handling Guide**: Both documents respond to Judge verdicts. A consolidated guide showing all verdicts and their handlers would reduce redundancy and ensure consistency.

2. **Extract "User Escalation" as a micro-workflow/skill**: Both documents involve escalating to users. Extract common patterns (summary format, options presentation, resumption protocol) into a shared component.

---

# Summary of Findings

| Artifact | Classification | Verdict | Skill Suitable | Priority Issues |
|----------|---------------|---------|----------------|-----------------|
| revision-flow.md | State Machine (hybrid) | HYBRID | Yes (implemented) | Missing timeout/cancellation states; duplicate content with skill |
| phase-rollback.md | Procedure/Protocol | MISNAMED | Borderline | Missing phase matrix; no denial/error handling; misnamed |

## Top Recommendations (Prioritized)

1. **High**: Add explicit state diagram to revision-flow.md
2. **High**: Define all phase confirmation requirements in phase-rollback.md
3. **Medium**: Rename phase-rollback.md to "Phase Rollback Protocol"
4. **Medium**: Add timeout and cancellation handling to revision flow
5. **Medium**: Document handoff between revision flow and phase rollback
6. **Low**: Consolidate duplicate content between revision-flow.md and SKILL.md
7. **Low**: Create shared "User Escalation" micro-workflow
