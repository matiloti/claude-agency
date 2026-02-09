# Workflow Analysis: Meta-Artifacts (Analyst 5)

**Analyst**: workflow-expert
**Date**: 2026-01-27
**Artifacts Analyzed**:
1. `system/orchestration/workflow-skill-mapping.md`
2. `system/orchestration/continuous-improvement.md`
3. `system/orchestration/workflows/README.md`
4. `system/orchestration/pipelines/README.md`

---

## Artifact 1: workflow-skill-mapping.md

### Classification
- **Current Name**: "Workflow-Skill Mapping"
- **Correct Classification**: Reference document / Registry
- **Verdict**: CORRECT (not a process, correctly not named as workflow/pipeline)
- **Recommendation**: None needed

### Accuracy Assessment

| Claim | Verified | Evidence |
|-------|----------|----------|
| Standard Feature Flow -> N/A | TRUE | Multi-session, 14 steps per standard-feature-flow.md |
| Quick Fix Flow -> `/quick-fix` | TRUE | Skill exists at `.claude/skills/quick-fix/SKILL.md` |
| Revision Flow -> `/revision` | TRUE | Skill exists at `.claude/skills/revision/SKILL.md` |
| Parallel Work Conflict Protocol -> N/A | TRUE | Triggered mid-flow, context-dependent |
| Architecture Amendment Flow -> N/A | TRUE | Triggered mid-flow per architecture-amendment-flow.md |
| Workflow Phase Rollback -> N/A | TRUE | Requires orchestrator judgment per phase-rollback.md |
| Dynamic Workflow Classification -> N/A | TRUE | Entry-point logic in workflows/README.md |

### Completeness Issues

**MAJOR: Missing Skills in Mapping Table**

The mapping table lists 4 skills in the "Skill Locations" section but only maps 2 of them (quick-fix and revision) in the main table:

| Skill | In Mapping Table | In Skill Locations |
|-------|-----------------|-------------------|
| `/quick-fix` | YES | YES |
| `/revision` | YES | YES |
| `/feedback-pipeline` | NO | YES |
| `/quick-feedback-capture` | NO | YES |

Both `/feedback-pipeline` and `/quick-feedback-capture` are listed in the Skill Locations section but not in the Mapping Table. This creates ambiguity: are they mapped to workflows or not?

**Recommendation**: Add entries to the Mapping Table for feedback-pipeline and quick-feedback-capture, either:
- Mapping them to the "Feedback Processing Flow" from continuous-improvement.md
- Or explicitly stating why they're standalone skills without workflow counterparts

**MINOR: Skill Inventory Incomplete**

The actual `.claude/skills/` directory contains 15 SKILL.md files, but workflow-skill-mapping.md only lists 4. While not all skills need workflow mappings, the document doesn't clarify this scope limitation.

Skills not mentioned:
- expo-api-routes, expo-tailwind-setup, frontend-design, ios-simulator-skill-xcode, ios-ux-design, mobile-ios-design, react-native-architecture, supabase-postgres-best-practices, vercel-react-best-practices, test-ios, project-onboard

**Recommendation**: Add a clarification: "This document maps only orchestration workflows to skills. Tech-specific skills (e.g., ios-ux-design, react-native-architecture) are documented separately and do not have corresponding workflows."

### Cross-Reference Issues

**MINOR: Inconsistent Path References**

The document references:
- `knowledge/orchestration/workflows/` - But workflows are actually at `system/orchestration/workflows/`
- `.claude/skills/*/SKILL.md` - Correct

The `knowledge/orchestration/workflows/` path appears to be legacy (no files found there). The document should reference `system/orchestration/workflows/`.

**Recommendation**: Update "Related Documentation" section:
```markdown
- Workflow definitions: `system/orchestration/workflows/`
- Skill pattern reference: `.claude/skills/*/SKILL.md`
- Orchestration rules: `CLAUDE.base.md` (Orchestration Rules section)
```

### Terminology Assessment

- Uses "workflow" and "skill" correctly throughout
- Good explanation of skill criteria (bounded, self-contained, deterministic, atomic)
- Clear rationale for what makes workflows unsuitable for skill conversion

---

## Artifact 2: continuous-improvement.md

### Classification
- **Current Name**: "Continuous Improvement Workflow"
- **Correct Classification**: WORKFLOW
- **Verdict**: CORRECT
- **Rationale**: Multi-actor coordination (Orchestrator, Retrospective Agent, Infrastructure Engineer, Judge), decision points (user approval), potential iteration in Learning Collection Flow

### Completeness Assessment

#### States Defined

The document describes states implicitly but not explicitly. Extracting the state machine:

**Feedback Processing Flow States:**
1. Intake (feedback exists in feedbacks/)
2. Analyzing (Retrospective Agent working)
3. Awaiting Approval (presented to user)
4. Snapshotting (Infrastructure Engineer)
5. Implementing (appropriate agent)
6. Reviewing (Judge)
7. Verifying (post-implementation check)
8. Complete OR Reverted

**MAJOR: Missing State Machine Definition**

Unlike workflows/README.md which has an explicit state machine table, continuous-improvement.md has no formal state definitions. The Feedback Lifecycle States section references `feedbacks/README.md` but doesn't include the states directly.

**Recommendation**: Add a state machine table similar to workflows/README.md:

```markdown
## Feedback Processing State Machine

| State | Meaning | Valid Transitions |
|-------|---------|-------------------|
| `pending` | Feedback added, not reviewed | -> `analyzing` |
| `analyzing` | Retrospective Agent evaluating | -> `approved`, `rejected` |
| `approved` | User approved implementation | -> `snapshotting` |
| `snapshotting` | Taking pre-change snapshot | -> `implementing` |
| `implementing` | Agent(s) making changes | -> `reviewing` |
| `reviewing` | Judge evaluating changes | -> `completed`, `reverted` |
| `completed` | All changes verified working | (terminal) |
| `reverted` | Changes rolled back | (terminal) |
```

#### Transitions Defined

Transitions are implicit in the 7-step flow description but not explicit. For example:
- What triggers movement from "Analyzing" to "Awaiting Approval"?
- What happens if user rejects during approval?

**MINOR: Missing Rejection Path**

Step 3 says "User approves which items to implement" but doesn't specify what happens if user rejects all items. The workflow assumes approval.

**Recommendation**: Add: "If user rejects all items, mark feedback batch as `rejected` in status.md and document rejection reason."

### Cross-Reference Issues

**CRITICAL: Broken Reference to feedbacks/README.md**

The document states:
> See `feedbacks/README.md` for detailed guidance...

But `feedbacks/README.md` exists only at the project level (`projects/flashcards-app/feedbacks/README.md`, `projects/spend-manager/feedbacks/README.md`), not at `system/orchestration/`. This is a structural issue: continuous-improvement.md is in `system/orchestration/` (cross-project) but references project-specific artifacts.

**Recommendation**: Either:
1. Create a canonical `system/orchestration/feedbacks-reference.md` that documents the format (DRY: single source)
2. Or change the reference to: "See `{project-root}/feedbacks/README.md` for project-specific implementation details"

**MINOR: Orphaned Orchestration Rule References**

Lines 87-89 reference "Rule 17, Rule 18, Rule 20" from CLAUDE.md. These rules exist in CLAUDE.base.md at lines 262-265. The reference should specify CLAUDE.base.md since continuous-improvement.md is at the system level, not project level.

### Terminology Assessment

- "Workflow" is correctly used for the multi-step coordination process
- "Flow" is used loosely ("Feedback Processing Flow", "Learning Collection Flow") - these are sub-workflows, correctly nested
- "Pipeline" is NOT used here, which is good since this has decision points and isn't linear

---

## Artifact 3: workflows/README.md

### Classification
- **Current Name**: "Orchestration Workflows" (index document)
- **Correct Classification**: Index/Reference document
- **Verdict**: CORRECT

### Accuracy Assessment

#### Table of Contents Verification

| Workflow | Listed | File Exists | Consistent |
|----------|--------|-------------|------------|
| Standard Feature Flow | YES | YES | YES |
| Quick Fix Flow | YES | YES | YES |
| Revision Flow | YES | YES | YES |
| Parallel Work Protocol | YES | YES | YES |
| Architecture Amendment | YES | YES | YES |
| Phase Rollback | YES | YES | YES |

All 6 workflows listed in the TOC exist as files in the workflows/ directory.

#### Judge Verdict Reference Verification

Verdicts documented: APPROVED, NEEDS_REVISION, REJECTED, ROLLBACK_TO_PHASE_{N}

Cross-checked against:
- revision-flow.md - Uses NEEDS_REVISION, APPROVED, REJECTED, ROLLBACK_TO_PHASE_{N} - CONSISTENT
- quick-fix-flow.md - Uses APPROVED, NEEDS_REVISION, REJECTED - CONSISTENT
- phase-rollback.md - Uses ROLLBACK_TO_PHASE_{N} - CONSISTENT

**Verdict reference is accurate and consistent.**

#### State Machine Definition Verification

| Status | In README | Used In Workflows | Consistent |
|--------|-----------|-------------------|------------|
| `pending` | YES | standard-feature-flow.md, revision-flow.md | YES |
| `in_progress` | YES | standard-feature-flow.md | YES |
| `completed` | YES | standard-feature-flow.md | YES |
| `blocked` | YES | standard-feature-flow.md, parallel-work-protocol.md | YES |
| `failed` | YES | standard-feature-flow.md | YES |

**State machine is consistent across documents.**

### Completeness Issues

**MINOR: Missing Workflow - Continuous Improvement**

The Table of Contents doesn't list "Continuous Improvement Workflow" even though it exists at `../continuous-improvement.md`. This workflow is part of the orchestration system but isn't indexed.

**Recommendation**: Either:
1. Add to TOC: `| Continuous Improvement | [../continuous-improvement.md](../continuous-improvement.md) | System learning, feedback processing |`
2. Or clarify scope: "This folder contains feature-development workflows. System-maintenance workflows (continuous improvement, analysis pipelines) are documented separately."

**MINOR: Dynamic Workflow Classification Not Cross-Referenced**

The Dynamic Workflow Classification section is comprehensive but doesn't reference the corresponding skill mapping. Adding a note would help:

```markdown
> See `../workflow-skill-mapping.md` for which workflows have skill implementations.
```

### Cross-Reference Issues

**MAJOR: Inconsistent Skill Path Reference**

Line 142 states:
> See `knowledge/orchestration/workflow-skill-mapping.md` for the complete mapping...

But the file is at `system/orchestration/workflow-skill-mapping.md`. This is the same error found in workflow-skill-mapping.md itself.

**Recommendation**: Update to `../workflow-skill-mapping.md` (relative) or `system/orchestration/workflow-skill-mapping.md` (absolute).

### Terminology Assessment

**Observation: "Protocol" vs "Workflow" vs "Flow"**

The document uses three terms:
1. "Workflow" - Standard Feature Flow, Quick Fix Flow, Revision Flow
2. "Protocol" - Parallel Work Conflict Protocol
3. "Flow" suffix - used for 4 of the 6 items

| Item | Named As | Characteristics | Correct Term? |
|------|----------|-----------------|---------------|
| Standard Feature Flow | Flow | Multi-actor, branching, 14 steps | Workflow - CORRECT |
| Quick Fix Flow | Flow | 4 steps, linear with gate | Borderline pipeline, but has decision point at Step 0 |
| Revision Flow | Flow | Iteration up to 3x, decision points | Workflow - CORRECT |
| Parallel Work Protocol | Protocol | Event-driven, coordination rules | Protocol is appropriate (not a workflow) |
| Architecture Amendment | Flow (implied) | Triggered mid-flow, judgment required | Workflow - CORRECT |
| Phase Rollback | N/A | Triggered by verdict, rule-based | Could be "Protocol" for consistency |

**MINOR Recommendation**: Consider renaming "Phase Rollback" to "Phase Rollback Protocol" for consistency with "Parallel Work Conflict Protocol" since both are triggered mid-workflow and follow rule-based coordination patterns.

---

## Artifact 4: pipelines/README.md

### Classification
- **Current Name**: "Orchestration Pipelines" (index document)
- **Correct Classification**: Index/Reference document
- **Verdict**: CORRECT

### Accuracy Assessment

#### Table of Contents Verification

| Pipeline | Listed | File Exists |
|----------|--------|-------------|
| Agent Analysis Pipeline | YES | YES |
| Workflow Analysis Pipeline | YES | YES |

Both pipelines listed exist in the directory.

#### Pipeline vs Workflow Distinction Verification

The document states pipelines are for:
- Linear, staged processes with clear phase progression
- Analysis and review processes
- Sequential transformations

Checked against actual pipeline files:

**agent-analysis-pipeline.md:**
- 5 phases (Distributed Analysis -> Report Synthesis -> Report Review -> Final Report -> Implementation)
- Each phase has clear entry/exit
- Output of each phase feeds next
- **Verdict**: Correctly classified as pipeline

**workflow-analysis-pipeline.md:**
- 5 phases (same structure)
- Linear progression
- Gates between phases (wait for all analysts)
- **Verdict**: Correctly classified as pipeline

**Terminology is correctly applied.**

### Completeness Issues

**MINOR: No State Machine Definition**

Unlike workflows/README.md, pipelines/README.md has no explicit state machine for pipeline phases. While pipelines are simpler (linear), documenting valid states would be consistent:

**Recommendation**: Add:
```markdown
## Pipeline Phase States

| State | Meaning | Valid Transitions |
|-------|---------|-------------------|
| `pending` | Phase not started | -> `in_progress` |
| `in_progress` | Subagents working | -> `completed`, `failed` |
| `completed` | Phase finished | -> next phase's `pending` |
| `failed` | Phase failed | -> `in_progress` (retry) or escalate |
```

**MINOR: No Cross-Reference to Workflow-Skill Mapping**

Pipelines are analysis processes that could theoretically become skills (bounded, deterministic). The document doesn't reference workflow-skill-mapping.md to clarify whether pipelines can/should be skills.

**Recommendation**: Add note:
> Pipelines are currently documentation-only and not implemented as skills. Unlike workflows, pipelines have deterministic structure that could make them skill candidates. See `../workflow-skill-mapping.md` for skill criteria.

### Cross-Reference Issues

**MINOR: Relative Path Assumptions**

The Related Documentation section uses relative paths:
- `../workflows/README.md` - Correct
- `../continuous-improvement.md` - Correct
- `../session-log-format.md` - Correct

All paths resolve correctly. No issues.

### Terminology Assessment

The document correctly distinguishes:
- **Pipelines**: Linear stages, gates, sequential
- **Workflows**: Branching, multi-actor, iterative

This matches the workflow-expert terminology reference exactly.

---

## Cross-Artifact Analysis

### Path Consistency Issues (Cross-Cutting)

| Document | References | Correct Path |
|----------|------------|--------------|
| workflow-skill-mapping.md | `knowledge/orchestration/workflows/` | `system/orchestration/workflows/` |
| workflows/README.md | `knowledge/orchestration/workflow-skill-mapping.md` | `system/orchestration/workflow-skill-mapping.md` |
| continuous-improvement.md | `feedbacks/README.md` | `{project-root}/feedbacks/README.md` |

**CRITICAL**: The system uses two orchestration path conventions:
1. `system/orchestration/` - Cross-project (current, correct)
2. `knowledge/orchestration/` - Legacy or project-specific (inconsistent)

**Recommendation**: Audit all files and standardize on `system/orchestration/` for cross-project artifacts. Add a note that project-specific customizations go in `{project}/knowledge/orchestration/`.

### Terminology Consistency (Cross-Cutting)

| Term | workflow-skill-mapping | continuous-improvement | workflows/README | pipelines/README |
|------|------------------------|------------------------|------------------|------------------|
| Workflow | Used correctly | Used correctly | Used correctly | Distinguishes from pipeline |
| Pipeline | Not used | Not used (correct) | Not used | Used correctly |
| Flow | N/A | Used for sub-workflows | Used as suffix | Not used |
| Skill | Used correctly | Not used | Referenced | Not used |
| Protocol | Not used | Not used | Used for Parallel Work | Not used |

**Observation**: "Protocol" is used only once (Parallel Work Conflict Protocol) but serves a valid purpose: distinguishing coordination rules from full workflows.

**Recommendation**: Add "Protocol" to the terminology reference in workflow-expert.md:
> **Protocol**: Coordination rules triggered by events during workflow execution; not a standalone workflow but defines response patterns.

### State Machine Consistency (Cross-Cutting)

- workflows/README.md has explicit state machine (pending, in_progress, completed, blocked, failed)
- continuous-improvement.md references feedbacks/README.md for states
- pipelines/README.md has no state machine definition

**Recommendation**: Ensure all process documents either:
1. Define their own state machine, OR
2. Explicitly reference the canonical state machine location

---

## Summary of Issues

### Critical (2)
1. **Broken reference**: continuous-improvement.md references `feedbacks/README.md` which doesn't exist at system level
2. **Inconsistent paths**: Multiple documents use `knowledge/orchestration/` instead of `system/orchestration/`

### Major (3)
1. **Missing skills in mapping**: feedback-pipeline and quick-feedback-capture not in mapping table
2. **Missing state machine**: continuous-improvement.md lacks explicit state definition
3. **Inconsistent skill path**: workflows/README.md references wrong path for workflow-skill-mapping.md

### Minor (7)
1. Skill inventory incomplete (only 4 of 15 skills documented)
2. No rejection path in continuous improvement workflow
3. Continuous improvement not listed in workflows/README.md TOC
4. Phase Rollback naming inconsistent with Parallel Work Protocol
5. No state machine in pipelines/README.md
6. No cross-reference to workflow-skill mapping from pipelines
7. "Protocol" term not in official terminology reference

### Observations (2)
1. "Flow" used as suffix inconsistently (some have it, some don't)
2. Document ownership unclear (continuous-improvement vs project feedbacks)

---

## Recommendations Summary

### Immediate Actions (Critical + Low Effort)

1. **Fix path references**: Global search and replace `knowledge/orchestration/` -> `system/orchestration/` in all system-level documents

2. **Clarify feedbacks/README.md scope**: Add to continuous-improvement.md:
   > Note: Feedback artifacts are stored at project level (`{project}/feedbacks/`). See each project's `feedbacks/README.md` for format details.

### Short-term Actions (Critical + Medium, Major + Low)

3. **Complete workflow-skill-mapping.md**: Add entries for feedback-pipeline and quick-feedback-capture

4. **Add state machine to continuous-improvement.md**: Define explicit states matching the feedback lifecycle

5. **Fix workflows/README.md skill reference**: Update path from `knowledge/orchestration/workflow-skill-mapping.md` to `../workflow-skill-mapping.md`

### Medium-term Actions (Major + Medium)

6. **Add state machine to pipelines/README.md**: Mirror the workflows/README.md format

7. **Clarify skill inventory scope**: Add note to workflow-skill-mapping.md explaining why only 4 of 15 skills are listed

### Backlog (Minor)

8. Consider renaming "Phase Rollback" to "Phase Rollback Protocol"
9. Add "Protocol" to workflow-expert terminology reference
10. Index continuous-improvement.md in workflows/README.md (or clarify scope)

---

## Skill Candidacy Assessment

| Document | Suitable for Skill? | Rationale |
|----------|---------------------|-----------|
| workflow-skill-mapping.md | NO | Reference document, not a process |
| continuous-improvement.md | NO | Multi-session, requires user approval at multiple points, unbounded scope |
| workflows/README.md | NO | Reference document, not a process |
| pipelines/README.md | NO | Reference document, not a process |

None of these meta-artifacts should become skills - they are documentation/reference materials, not executable processes.
