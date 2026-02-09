# Agent Analysis Pipeline

## Purpose

Systematically analyze all agent specifications and prompt templates to identify improvements, eliminate redundancies, and optimize for conciseness while preserving precision.

## When to Use

- Periodic system health checks
- After adding multiple new agents
- Before major system refactoring
- When agents show inconsistent behavior

## Workflow Steps

### Phase 1: Distributed Analysis

**Leader**: Spawns 5 agent-specificator subagents in parallel.

Each subagent receives:
- A subset of agents to analyze (2-3 agents each)
- Both the agent specification AND corresponding prompt template
- Instructions to produce feedback on:
  - Strengths (what works)
  - Issues (CRITICAL/MAJOR/MINOR with fixes)
  - Duplications (spec vs template misplacement)
  - Naming assessment (appropriate/vague/wrong)
  - Condensation opportunities

**Output**: Each subagent returns structured feedback per agent.

### Phase 2: Report Synthesis

**Leader**: Collects all feedback and synthesizes into a unified report.

Actions:
1. Aggregate all findings by agent
2. Identify cross-agent patterns (issues appearing in multiple agents)
3. Prioritize by impact (CRITICAL > MAJOR > MINOR)
4. Create draft report following the Report Structure

### Phase 3: Report Review

**Leader**: Spawns 5 agent-specificator subagents in parallel to review the draft report.

Each reviewer evaluates:
- Are findings accurate? (not based on misunderstanding)
- Are recommendations actionable? (specific enough to implement)
- Should any items be discarded? (false positives, low value)
- Should any items be modified? (better phrasing, different severity)

**Constraints for reviewers**:
- DO NOT modify just because "it's your job"
- Only suggest changes with clear justification
- Preserve valuable findings even if imperfect phrasing

**Output**: Each reviewer returns:
```
## Review of Agent Analysis Report

### Items to KEEP (unchanged)
- [item ref]: [why it's correct]

### Items to MODIFY
- [item ref]: [current] → [suggested] - [justification]

### Items to DISCARD
- [item ref]: [why it should be removed]

### Items MISSING
- [new finding not in report]
```

### Phase 4: Final Report

**Leader**: Incorporates review feedback and produces final report.

Actions:
1. Apply modifications with clear justification
2. Remove discarded items (only with strong justification)
3. Add missing items from reviewers
4. Write final report to designated folder

### Phase 5: Implementation

**Leader**: Spawns one agent-specificator subagent per agent in the report.

Each implementer:
- Receives the final report section for their assigned agent
- Applies approved changes to the agent specification
- Applies approved changes to the prompt template
- Creates implementation summary

## Report Structure

```markdown
# Agent Analysis Report

**Generated**: {ISO timestamp}
**Agents Analyzed**: {count}
**Analysts**: {count in phase 1}
**Reviewers**: {count in phase 3}

## Executive Summary

### Overall Health
- Total Issues: {count by severity}
- Agents Needing Attention: {list}
- Cross-Cutting Patterns: {count}

### Priority Actions
1. [highest impact change]
2. [second highest]
3. [third highest]

## Cross-Agent Patterns

### Pattern: {name}
- **Affected Agents**: {list}
- **Issue**: {description}
- **Recommendation**: {unified fix}

## Agent-by-Agent Analysis

### {Agent Name}

#### Summary
{1-2 sentences}

#### Naming
- Current: `{name}`
- Verdict: {APPROPRIATE | NEEDS_REFINEMENT | WRONG}
- Recommendation: {if needed}

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| CRITICAL | ... | ... | ... |
| MAJOR | ... | ... | ... |
| MINOR | ... | ... | ... |

#### Duplication Analysis
- **Move to template**: {list}
- **Move to spec**: {list}
- **Consolidate**: {list}

#### Condensation
- Before: {verbose version}
- After: {concise version}
- Tokens saved: ~{estimate}

## Implementation Checklist

- [ ] {Agent 1}: {summary of changes}
- [ ] {Agent 2}: {summary of changes}
...

## Appendix: Review Decisions

### Accepted Modifications
- {item}: {original} → {modified} - {reviewer justification}

### Discarded Items
- {item}: {reason for removal}

### Added Items
- {item}: {reviewer who identified it}
```

## Artifact Location

All artifacts go to:
```
claude-agency/system/cross-project-analysis/{timestamp}_agent-analysis/
  - final-report.md
  - phase1-feedback/
    - analyst-1-feedback.md
    - analyst-2-feedback.md
    - ...
  - phase3-reviews/
    - reviewer-1-feedback.md
    - reviewer-2-feedback.md
    - ...
  - implementation-log.md
```

## Timing

- Phase 1: Parallel (wait for all)
- Phase 2: Sequential (leader only)
- Phase 3: Parallel (wait for all)
- Phase 4: Sequential (leader only)
- Phase 5: Parallel (wait for all) - OPTIONAL, requires approval
