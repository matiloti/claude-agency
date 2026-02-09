# Workflow Expert Analysis - Analyst 3

**Date**: 2026-01-27
**Analyst**: Workflow Expert
**Artifacts Analyzed**:
1. `/Users/matias/Projects/claude-agency/system/orchestration/workflows/parallel-work-protocol.md`
2. `/Users/matias/Projects/claude-agency/system/orchestration/workflows/architecture-amendment-flow.md`

---

# Process Analysis: Parallel Work Conflict Protocol

## Classification
- **Current Name**: "Parallel Work Conflict Protocol"
- **Correct Classification**: **Workflow** (with embedded state machine characteristics)
- **Verdict**: HYBRID
- **Recommendation**: Rename to "Parallel Work Coordination Workflow" and explicitly document the embedded state machine for blocker handling.

### Evidence for Classification:
The artifact exhibits clear workflow characteristics:
- Multiple actors (parallel agents, orchestrator, architect, judge)
- Decision points requiring judgment (blocker prioritization)
- Branching paths (blocked vs. unblocked work)
- Iteration potential (resume after amendment)

However, "protocol" as a suffix is unusual. Protocols typically define communication standards or behavioral contracts, not process flows. The document actually defines a **workflow** for handling conflicts during parallel execution.

## Structure Summary
- **States/Phases**: 4 implicit states (Normal Parallel Work, Blocker Discovered, Amendment Pending, Work Resumed)
- **Transitions**: 5 implicit transitions
- **Decision Points**: 2 (blocker classification, simultaneous blocker prioritization)
- **Actors**: Parallel agents (Frontend/Backend), Orchestrator, Architect, Judge

## Gap Analysis

### Missing States
- **"Amendment Rejected"**: No state defined for what happens if the Judge rejects the architect's amendment. The flow assumes approval but doesn't handle rejection.
- **"Partial Completion"**: No explicit state for when an agent completes some work but is blocked on other parts.
- **"Both Blocked"**: When both agents report blockers simultaneously, this deserves explicit state recognition.

### Missing Transitions
- **"Amendment Rejected" -> "Re-Amendment"**: What happens after Judge rejection? Does Architect revise? Is the feature abandoned?
- **"Blocker Resolved" -> "Agent Re-engagement"**: How does a blocked agent receive notification and resume? File polling? Direct notification?
- **"Simultaneous Blockers" -> "Second Blocker Handling"**: After first blocker is resolved, how is the second one queued/handled?

### Undefined Decision Points
- **Blocker severity assessment**: Who determines if a blocker requires full stop vs. partial continuation? The agent? The orchestrator?
- **Clarification vs. Amendment threshold**: When does an ambiguity become a blocker requiring formal amendment vs. a simple clarification request?
- **Agent re-read requirements**: After an amendment, does the resuming agent need to re-read all architecture files or just the amendment?

## Redundancy Analysis

### Duplicate Logic
- **Handoff creation**: Section 3 mentions handoffs are created "upon completion of each parallel track," but this is also covered in the Standard Feature Flow and CLAUDE.md. The handoff mechanism is defined in multiple places with potentially inconsistent detail.

### Unreachable Elements
- None identified. All described states can be reached given the described conditions.

## Optimization Opportunities

### Structural
1. **Extract blocker state machine**: Create an explicit state machine for blocker states (None -> Discovered -> Documented -> AmendmentPending -> Resolved | Escalated). This would clarify transitions and ensure all paths are covered.
2. **Merge with architecture amendment flow**: Sections 2 and 4 heavily reference the architecture amendment flow. Consider whether this should be a sub-workflow of parallel-work-protocol or remain separate but with clearer integration points.
3. **Add escalation path**: Define what happens if an amendment cycle fails repeatedly (e.g., 3 rejected amendments -> escalate to human review).

### Naming
1. **"Parallel Work Conflict Protocol"** -> **"Parallel Work Coordination Workflow"**: "Protocol" suggests communication standards; this is a coordination workflow.
2. **"Discovery of Blocking Issue"** -> **"Blocker Discovery Phase"**: Align with phase-based naming convention used elsewhere.

### Skill Candidacy
- **Suitable for skill?**: **No**
- **Rationale**: This workflow requires mid-execution judgment calls (prioritization, classification), involves multiple actors with handoffs, and has non-deterministic paths based on blocker types. These characteristics violate skill requirements for bounded scope and deterministic behavior. However, individual sub-processes (like "Document Blocker") could potentially be extracted as skills.

## Recommendations
1. **Add explicit state machine diagram**: Document states (Normal, BlockerDiscovered, AmendmentPending, Resolved) with explicit transitions and guards.
2. **Define rejection handling**: Add a section "5. Amendment Rejection" that describes the path when Judge rejects an amendment.
3. **Clarify notification mechanism**: Specify HOW agents are notified of resolution (file creation, polling pattern, or orchestrator message).
4. **Add timeout handling**: Define what happens if an amendment takes too long (SLA for architect response, escalation path).
5. **Cross-reference clarification threshold**: Add explicit reference to the Flaw Classification Rubric from architecture-amendment-flow.md to avoid duplicate decision-making logic.

---

# Process Analysis: Architecture Amendment Flow

## Classification
- **Current Name**: "Architecture Amendment Flow"
- **Correct Classification**: **Pipeline** (with initial decision gate)
- **Verdict**: MISNAMED
- **Recommendation**: Rename to "Architecture Amendment Pipeline" since it's linear once the initial classification is made.

### Evidence for Classification:
After the initial Flaw Classification decision:
- Linear progression: Document -> Spawn Architect -> Produce Amendment -> Judge Review -> Notify
- Each stage has defined inputs (issue description) and outputs (amendment, approval)
- No loops or revisiting previous steps documented
- Predictable path once entered

The initial Flaw Classification Rubric is a decision gate, not a full workflow decision point. Once classification is made, the process is strictly linear.

## Structure Summary
- **States/Phases**: 5 explicit steps + 1 classification gate
- **Transitions**: 5 (each step to next)
- **Decision Points**: 2 (Flaw Classification, Judge approval - though approval path incomplete)
- **Actors**: Developer, Orchestrator, Architect, Judge, Affected Agents

## Gap Analysis

### Missing States
- **"Amendment Rejected"**: Critical missing state. Step 4 says "If approved" but never addresses rejection.
- **"Amendment Revision"**: If rejected, does architect revise? Is there a revision cycle?
- **"Clarification Response"**: For "Clarification Only" classification, what state captures the clarification exchange?
- **"No Action Needed"**: For "Developer Misunderstanding," the flow ends but this isn't captured as a terminal state.

### Missing Transitions
- **"Judge Review" -> "Amendment Rejected"**: What triggers this? What happens next?
- **"Amendment Rejected" -> "Architect Revision"**: Is revision allowed? How many times?
- **"Amendment Rejected" -> "Escalation"**: At what point does rejection escalate beyond the normal cycle?
- **"Clarification Only" -> "Clarification Provided"**: The rubric mentions this path but doesn't document it.

### Undefined Decision Points
- **Judge rejection criteria**: What causes a Judge to reject an amendment? This affects whether developers and architects can predict outcomes.
- **Revision limits**: How many revision cycles are allowed before escalation?
- **Clarification authority**: For "Clarification Only," does Architect respond directly or via orchestrator mediation?
- **"Amendment Required" threshold examples**: The rubric is abstract. Concrete examples would help developers classify correctly.

## Redundancy Analysis

### Duplicate Logic
- **Developer documentation requirement**: Step 1 says developer documents in "completion summary." The parallel-work-protocol says documents under "Blockers Encountered." These may be the same thing but phrasing differs, creating ambiguity about where/how to document.

### Unreachable Elements
- **"Developer Misunderstanding" terminal**: This classification effectively exits the flow, but the exit isn't explicitly documented. Not unreachable per se, but under-documented.

## Optimization Opportunities

### Structural
1. **Add rejection loop**: Document the rejection -> revision -> re-review cycle with a maximum iteration count and escalation path.
2. **Expand Clarification Only path**: This is mentioned but not documented. If it's truly just "ask Architect," make that explicit with a lightweight process.
3. **Consolidate documentation location**: Standardize where blockers/issues are documented to avoid confusion with parallel-work-protocol.
4. **Add concrete examples**: The Flaw Classification Rubric would benefit from 2-3 concrete examples per category to aid developer judgment.

### Naming
1. **"Architecture Amendment Flow"** -> **"Architecture Amendment Pipeline"**: Once classification is made, this is linear with gates, not a multi-branch workflow.
2. **"Flaw Classification Rubric"** -> **"Amendment Decision Gate"**: Clarifies its role as an entry gate to the pipeline.

### Skill Candidacy
- **Suitable for skill?**: **Partial - Flaw Classification Only**
- **Rationale**:
  - **Full flow**: No. Involves multiple actors, requires Judge judgment, has potential for iteration.
  - **Flaw Classification Rubric**: Yes. This decision gate is bounded (3 outcomes), self-contained (single decision maker), deterministic (clear criteria), and atomic (classifies completely or requests more info). Could be extracted as a skill like "classify-architecture-flaw" that returns the classification.

## Recommendations
1. **Define rejection path**: Add Step 6 "If Rejected" with either revision cycle or escalation path.
2. **Add iteration limits**: Specify maximum revision attempts (e.g., "After 2 rejections, escalate to...").
3. **Document Clarification path**: Expand "Clarification Only" into a lightweight process (who to ask, how to document the answer, where to store it).
4. **Add examples to rubric**: Include concrete examples like "Adding a new query parameter to an existing endpoint = Amendment Required" vs. "Clarifying that 'date' field is ISO 8601 format = Clarification Only."
5. **Extract Flaw Classification as skill**: Create a `.claude/skills/classify-architecture-flaw.md` skill that takes issue description and returns classification with rationale.
6. **Rename to Pipeline**: Update naming to reflect the linear nature post-classification.

---

# Cross-Artifact Analysis

## Interdependencies
These two artifacts are tightly coupled:
- **parallel-work-protocol.md** Section 2 explicitly references architecture-amendment-flow.md
- Both deal with mid-implementation architectural issues
- Both involve the same actors (Developer, Orchestrator, Architect, Judge)

## Consistency Issues
1. **Documentation location**: parallel-work-protocol says "Blockers Encountered" section; architecture-amendment-flow says "completion summary." These should be explicitly aligned or clarified as the same location.
2. **Blocker vs. Flaw terminology**: parallel-work-protocol uses "blocking issue" and "blocker"; architecture-amendment-flow uses "flaw." Terminology should be consistent.
3. **Resolution notification**: parallel-work-protocol says orchestrator "notifies both agents"; architecture-amendment-flow says orchestrator "notifies affected agents." These are the same thing but phrased differently.

## Consolidation Opportunity
Consider merging these into a single document with sections:
1. Flaw/Blocker Classification (current rubric)
2. Parallel Work Coordination (for ongoing parallel work)
3. Amendment Process (the pipeline)
4. Rejection/Escalation Handling (currently missing from both)

This would eliminate cross-references, ensure terminology consistency, and provide a single source of truth for mid-implementation architecture issues.

---

# Summary Matrix

| Criterion | parallel-work-protocol.md | architecture-amendment-flow.md |
|-----------|---------------------------|--------------------------------|
| **Classification Correctness** | HYBRID (Workflow + implicit state machine) | MISNAMED (Flow -> Pipeline) |
| **Completeness** | Incomplete (missing rejection path, notification mechanism) | Incomplete (missing rejection path, clarification path) |
| **Correctness** | Mostly correct, no deadlocks identified | Potential deadlock: rejection with no exit |
| **Redundancy** | Handoff logic duplicated with other docs | Documentation location ambiguous with parallel-work-protocol |
| **Skill Candidacy** | No (too judgment-heavy) | Partial (Flaw Classification only) |
| **Priority Recommendations** | 1. Add rejection handling, 2. Clarify notification mechanism | 1. Define rejection path, 2. Rename to Pipeline |

---

**End of Analysis**
