# Workflow Analysis Pipeline

## Purpose

Systematically analyze all workflows, pipelines, flows, skills, and state machines to:
- Verify correct terminology (workflow vs pipeline vs flow vs state machine)
- Identify redundancies and duplications across process documentation
- Find gaps (missing states, transitions, exit conditions)
- Suggest merges, splits, or eliminations
- Evaluate folder structure and organization
- Assess workflow-skill mapping correctness

## When to Use

- Periodic system health checks (recommended: quarterly)
- After adding multiple new workflows or skills
- When workflow execution shows inconsistent behavior
- Before major orchestration refactoring
- When terminology confusion arises in agent prompts

## Scope

**Artifacts to analyze:**
- `system/orchestration/workflows/*.md` - Workflow definitions
- `system/orchestration/pipelines/*.md` - Pipeline definitions
- `.claude/skills/*/SKILL.md` - Skill implementations
- `system/orchestration/workflow-skill-mapping.md` - Mapping correctness
- `system/orchestration/continuous-improvement.md` - Feedback processing flow
- Project-specific customizations in `{project}/knowledge/orchestration/workflows/` (if present)

---

## Pipeline Phases

### Phase 1: Distributed Analysis

**Leader**: Spawns 5 workflow-expert subagents in parallel.

Each subagent receives:
- A subset of workflows/skills to analyze (2-3 each)
- The terminology reference from workflow-expert spec
- Instructions to produce feedback using the workflow-expert output format

**Subagent assignments** (example distribution):
- Analyst 1: standard-feature-flow.md, quick-fix-flow.md
- Analyst 2: revision-flow.md, phase-rollback.md
- Analyst 3: parallel-work-protocol.md, architecture-amendment-flow.md
- Analyst 4: Skills (quick-fix, revision, feedback-pipeline)
- Analyst 5: workflow-skill-mapping.md, continuous-improvement.md, README files

**Each analyst evaluates:**
1. **Classification correctness** - Is this named appropriately (workflow/pipeline/flow/state machine)?
2. **Completeness** - Are all states, transitions, decision criteria, and exit conditions defined?
3. **Correctness** - Can every state be reached and exited? Are there deadlocks or unreachable states?
4. **Redundancy** - Are there duplicate logic, parallel definitions, or repeated patterns?
5. **Optimization opportunities** - Could this be merged, split, simplified, or converted to a skill?
6. **Skill candidacy** - Does this meet the criteria for skill conversion?

**Output**: Each subagent returns structured feedback per artifact analyzed.

---

### Phase 2: Report Synthesis

**Leader**: Collects all feedback and synthesizes into a unified report.

Actions:
1. Aggregate all findings by artifact type (workflows, pipelines, skills, mappings)
2. Identify cross-artifact patterns (issues appearing in multiple places)
3. Detect inter-artifact conflicts (e.g., workflow references non-existent skill)
4. Prioritize by impact (CRITICAL > MAJOR > MINOR)
5. Create draft report following the Report Structure

**Cross-artifact checks:**
- Do workflow references resolve correctly?
- Is workflow-skill-mapping.md accurate and complete?
- Are state machine definitions consistent across README and individual files?
- Are exit conditions aligned between parent and sub-workflows?

---

### Phase 3: Report Review

**Leader**: Spawns 5 workflow-expert subagents in parallel to review the draft report.

Each reviewer evaluates:
- Are classification verdicts justified? (misnamed vs correctly named)
- Are gap findings real? (actually missing vs implied by context)
- Are redundancy claims valid? (true duplication vs intentional repetition)
- Are recommendations actionable? (specific enough to implement)
- Should any items be discarded? (false positives, low value)

**Constraints for reviewers:**
- DO NOT modify just because "it's your job"
- Only suggest changes with clear justification
- Preserve valuable findings even if imperfect phrasing
- Challenge recommendations that would add complexity without benefit

**Output**: Each reviewer returns:

```
## Review of Workflow Analysis Report

### Items to KEEP (unchanged)
- [item ref]: [why it's correct]

### Items to MODIFY
- [item ref]: [current] -> [suggested] - [justification]

### Items to DISCARD
- [item ref]: [why it should be removed]

### Items MISSING
- [new finding not in report]
```

---

### Phase 4: Final Report

**Leader**: Incorporates review feedback and produces final report.

Actions:
1. Apply modifications with clear justification
2. Remove discarded items (only with strong justification)
3. Add missing items from reviewers
4. Resolve conflicting reviewer opinions (favor conservative changes)
5. Write final report to designated folder

---

### Phase 5: Implementation

**Leader**: Spawns one prompt-engineer subagent per artifact requiring changes.

**Note**: This phase uses **prompt-engineer** subagents (not workflow-experts) because the implementation work is documentation optimization - condensing, restructuring, and clarifying process documentation.

Each implementer:
- Receives the final report section for their assigned artifact(s)
- Applies approved terminology corrections
- Adds missing states, transitions, or exit conditions
- Removes identified redundancies
- Restructures content for clarity
- Creates implementation summary

**Implementation constraints:**
- Never remove safety mechanisms (review steps, quality gates) without explicit approval
- Preserve existing behavior unless the report explicitly recommends changes
- Document all changes in implementation summary

---

## Report Structure

```markdown
# Workflow Analysis Report

**Generated**: {ISO timestamp}
**Artifacts Analyzed**: {count by type}
**Analysts**: {count in phase 1}
**Reviewers**: {count in phase 3}

## Executive Summary

### Overall Health
- Terminology Issues: {count by severity}
- Gap Issues: {count - missing states, transitions, conditions}
- Redundancy Issues: {count}
- Structural Issues: {count}

### Priority Actions
1. [highest impact change]
2. [second highest]
3. [third highest]

## Terminology Audit Results

### Correctly Named
| Artifact | Type | Rationale |
|----------|------|-----------|
| {name} | {workflow/pipeline/flow/state machine} | {why it fits} |

### Incorrectly Named
| Artifact | Current Name | Correct Name | Issue | Recommendation |
|----------|--------------|--------------|-------|----------------|
| {name} | "workflow" | "pipeline" | Linear stages, no branching | Rename and restructure |

## Gap Analysis

### Missing States
| Artifact | Missing State | Context | Impact |
|----------|--------------|---------|--------|
| {name} | {state} | {when needed} | {what breaks without it} |

### Missing Transitions
| Artifact | From | To | When | Impact |
|----------|------|-----|------|--------|
| {name} | {state} | {state} | {trigger} | {what breaks} |

### Undefined Exit Conditions
| Artifact | Issue | Recommendation |
|----------|-------|----------------|
| {name} | {what's undefined} | {specific fix} |

### Undefined Decision Points
| Artifact | Decision | Missing Info |
|----------|----------|--------------|
| {name} | {decision} | {what criteria needed} |

## Redundancy Analysis

### Duplicate Definitions
| Artifact A | Artifact B | Overlap | Recommendation |
|------------|------------|---------|----------------|
| {file} | {file} | {what's duplicated} | {merge/consolidate/reference} |

### Unreachable Elements
| Artifact | Element | Why Unreachable |
|----------|---------|-----------------|
| {name} | {state/transition} | {explanation} |

## Structural Recommendations

### Merges
| Artifacts | Rationale | New Structure |
|-----------|-----------|---------------|
| {list} | {why merge} | {proposed result} |

### Splits
| Artifact | Rationale | New Structure |
|----------|-----------|---------------|
| {name} | {why split} | {proposed components} |

### Eliminations
| Artifact | Rationale | Migration Path |
|----------|-----------|----------------|
| {name} | {why remove} | {how to migrate} |

### Folder Reorganization
| Current | Proposed | Rationale |
|---------|----------|-----------|
| {path} | {path} | {why move} |

## Workflow-Skill Mapping Recommendations

### Missing Mappings
| Workflow | Should Be Skill? | Rationale |
|----------|------------------|-----------|
| {name} | {yes/no} | {skill criteria evaluation} |

### Incorrect Mappings
| Current Mapping | Issue | Correction |
|-----------------|-------|------------|
| {workflow -> skill} | {problem} | {fix} |

### Mapping Table Updates
| Change Type | Item | Reason |
|-------------|------|--------|
| {add/remove/modify} | {entry} | {justification} |

## Artifact-by-Artifact Analysis

### {Artifact Name}

#### Summary
{1-2 sentences}

#### Classification
- **Current**: `{name}`
- **Correct Type**: {workflow | pipeline | flow | state machine}
- **Verdict**: {CORRECT | MISNAMED | HYBRID}
- **Recommendation**: {if misnamed}

#### Completeness
| Element | Status | Note |
|---------|--------|------|
| States defined | {complete/partial/missing} | {detail} |
| Transitions defined | {complete/partial/missing} | {detail} |
| Exit conditions | {defined/undefined} | {detail} |
| Decision criteria | {documented/undocumented} | {detail} |

#### Issues
| Severity | Issue | Fix |
|----------|-------|-----|
| CRITICAL | ... | ... |
| MAJOR | ... | ... |
| MINOR | ... | ... |

#### Skill Candidacy
- **Suitable for skill?**: {yes | no}
- **Rationale**: {skill criteria evaluation}

## Implementation Checklist

- [ ] {Artifact 1}: {summary of changes}
- [ ] {Artifact 2}: {summary of changes}
...

## Appendix: Review Decisions

### Accepted Modifications
- {item}: {original} -> {modified} - {reviewer justification}

### Discarded Items
- {item}: {reason for removal}

### Added Items
- {item}: {reviewer who identified it}
```

---

## Artifact Location

All artifacts go to:
```
claude-agency/system/cross-project-analysis/{timestamp}_workflow-analysis/
  - final-report.md
  - phase1-feedback/
    - analyst-1-feedback.md
    - analyst-2-feedback.md
    - analyst-3-feedback.md
    - analyst-4-feedback.md
    - analyst-5-feedback.md
  - phase3-reviews/
    - reviewer-1-feedback.md
    - reviewer-2-feedback.md
    - reviewer-3-feedback.md
    - reviewer-4-feedback.md
    - reviewer-5-feedback.md
  - implementation-log.md
```

---

## Timing

- Phase 1: Parallel (wait for all 5 analysts)
- Phase 2: Sequential (leader only)
- Phase 3: Parallel (wait for all 5 reviewers)
- Phase 4: Sequential (leader only)
- Phase 5: Parallel (wait for all) - OPTIONAL, requires user approval

---

## Related Documentation

- [Agent Analysis Pipeline](./agent-analysis-pipeline.md) - Similar structure for agent specs
- [Workflow-Skill Mapping](../workflow-skill-mapping.md) - Source of skill mapping data
- [Continuous Improvement](../continuous-improvement.md) - Feedback processing flow
- [Workflow Expert Agent](../../../.claude/agents/workflow-expert.md) - Analysis criteria
- [Prompt Engineer Agent](../../../.claude/agents/prompt-engineer.md) - Implementation criteria
