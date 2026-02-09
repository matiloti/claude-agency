# Prompt Analysis Pipeline

## Purpose

Systematically analyze all prompts, templates, and AI-facing documentation to:
- Identify clarity and precision issues
- Detect ambiguity that causes inconsistent AI behavior
- Find missing context or instructions
- Discover condensation opportunities to save tokens
- Eliminate redundancy across documents
- Ensure structural consistency

## When to Use

- Periodic prompt health checks (recommended: monthly)
- After adding multiple new prompts or templates
- When agents exhibit inconsistent behavior
- Before major system refactoring
- When token usage becomes a concern
- After feedback indicates prompt misinterpretation

## Scope

**Artifacts to analyze:**
- `.claude/agents/*.md` - Agent specifications
- `system/templates/prompts/*.md` - Prompt templates for orchestrator
- `CLAUDE.md` files - Project-level instructions
- `CLAUDE.base.md` - Base orchestrator configuration
- `.claude/skills/*/SKILL.md` - Skill definitions
- Any AI-facing documentation that instructs model behavior

**Exclusions:**
- Human-only documentation (READMEs explaining architecture)
- Session logs and reports
- Generated content

---

## Pipeline Phases

### Phase 1: Distributed Analysis

**Leader**: Spawns 5 prompt-engineer subagents in parallel.

Each subagent receives:
- A subset of prompts/documents to analyze (3-5 files each)
- The prompt-engineer analysis framework
- Instructions to evaluate each document systematically

**Subagent assignments** (example distribution for typical agency):
- Analyst 1: Core agents (architect, backend-developer, frontend-developer)
- Analyst 2: Supporting agents (judge, qa-tester, documentation)
- Analyst 3: Specialized agents (prompt-engineer, workflow-expert, ideator)
- Analyst 4: Prompt templates and CLAUDE.md files
- Analyst 5: Skills and cross-cutting configurations

**Each analyst evaluates:**

1. **Clarity Assessment**
   - Are instructions explicit or implicit?
   - Can any statement be interpreted multiple ways?
   - Are all constraints clearly stated?

2. **Precision Issues**
   - Vague terms that need definition ("appropriate", "good", "consider")
   - Hedged language that weakens instructions ("try to", "should")
   - Missing specificity ("handle errors" vs "log errors, then retry once")

3. **Ambiguity Detection**
   - Conflicting instructions within the document
   - Terms used inconsistently
   - Undefined jargon or acronyms
   - Unclear scope boundaries

4. **Context Completeness**
   - Missing prerequisites or assumptions
   - Undefined inputs or outputs
   - Unclear triggers or conditions
   - Missing examples for complex rules

5. **Structure Evaluation**
   - Prose vs structured format (tables, lists, headers)
   - Scannability (can key info be found in 10 seconds?)
   - Logical organization of sections
   - Appropriate use of markdown features

6. **Condensation Opportunities**
   - Redundant explanations
   - Verbose phrasing that can be tightened
   - Repeated instructions across sections
   - Over-explanation of simple concepts

**Output**: Each subagent returns structured feedback per document.

---

### Phase 2: Report Synthesis

**Leader**: Collects all feedback and synthesizes into a unified report.

Actions:
1. Aggregate all findings by document
2. Identify cross-file patterns (issues appearing in multiple prompts)
3. Detect terminology inconsistencies across documents
4. Prioritize by impact (CRITICAL > MAJOR > MINOR)
5. Calculate estimated token savings from condensation
6. Create draft report following the Report Structure

**Cross-file checks:**
- Is terminology consistent across all prompts?
- Are referenced documents/sections valid?
- Do agent specs align with their prompt templates?
- Are constraints consistently enforced?

---

### Phase 3: Report Review

**Leader**: Spawns 5 different prompt-engineer subagents in parallel to review the draft report.

Each reviewer evaluates:
- Are findings accurate? (not based on misunderstanding the document's purpose)
- Are recommendations actionable? (specific enough to implement)
- Are severity ratings justified? (CRITICAL vs MAJOR vs MINOR)
- Would the suggested changes preserve original intent?
- Should any items be discarded? (false positives, low value, context-dependent choices)
- Should any items be modified? (better phrasing, different approach)

**Constraints for reviewers:**
- DO NOT modify just because "it's your job"
- Only suggest changes with clear justification
- Preserve valuable findings even if imperfect phrasing
- Challenge recommendations that sacrifice precision for brevity
- Verify before/after examples actually improve the prompt

**Output**: Each reviewer returns:

```
## Review of Prompt Analysis Report

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
2. Remove discarded items (only with strong justification from multiple reviewers)
3. Add missing items from reviewers
4. Resolve conflicting reviewer opinions (favor conservative changes)
5. Recalculate token savings estimates
6. Write final report to designated folder

---

### Phase 5: Implementation (Optional)

**Leader**: Spawns prompt-engineer subagents to implement approved changes.

**Note**: This phase requires explicit user approval before execution.

Each implementer:
- Receives the final report section for their assigned document(s)
- Applies approved condensation and restructuring
- Clarifies ambiguous language
- Adds missing context or constraints
- Preserves original intent throughout
- Creates implementation summary with before/after comparisons

**Implementation constraints:**
- Never remove safety constraints or validation rules
- Never introduce new ambiguity while fixing old ambiguity
- Preserve all behavioral constraints even if reworded
- Document all changes in implementation summary

---

## Report Structure

```markdown
# Prompt Analysis Report

**Generated**: {ISO timestamp}
**Documents Analyzed**: {count}
**Analysts**: {count in phase 1}
**Reviewers**: {count in phase 3}

## Executive Summary

### Overall Health
- Critical Issues: {count}
- Major Issues: {count}
- Minor Issues: {count}
- Documents Needing Attention: {list}
- Estimated Token Savings: ~{tokens} ({percentage}%)

### Priority Actions
1. [highest impact change]
2. [second highest]
3. [third highest]

## Cross-File Patterns

### Pattern: {name}
- **Affected Documents**: {list}
- **Issue**: {description}
- **Unified Fix**: {recommendation}
- **Impact**: {why this matters}

### Terminology Inconsistencies
| Term | Document A | Document B | Recommended Standard |
|------|------------|------------|---------------------|
| {term} | {usage} | {different usage} | {standardized definition} |

## Document-by-Document Analysis

### {Document Name}

#### Summary
{1-2 sentences on overall quality}

#### Clarity Score
- **Rating**: {HIGH | MEDIUM | LOW}
- **Justification**: {brief explanation}

#### Issues

| Severity | Category | Issue | Location | Fix |
|----------|----------|-------|----------|-----|
| CRITICAL | Ambiguity | {description} | Line {n} | {specific fix} |
| MAJOR | Precision | {description} | Section {x} | {specific fix} |
| MINOR | Structure | {description} | {location} | {specific fix} |

#### Ambiguity Analysis
- **Conflicting Instructions**: {list or "None found"}
- **Undefined Terms**: {list or "None found"}
- **Multiple Interpretations**: {list or "None found"}

#### Missing Context
- **Prerequisites**: {what's missing}
- **Examples**: {where examples would help}
- **Edge Cases**: {undocumented scenarios}

#### Condensation Opportunities

| Current | Proposed | Tokens Saved |
|---------|----------|--------------|
| "{verbose text}" | "{concise text}" | ~{n} |

#### Structure Recommendations
- {specific structural improvement}

## Implementation Checklist

- [ ] {Document 1}: {summary of changes} - Est. savings: ~{tokens}
- [ ] {Document 2}: {summary of changes} - Est. savings: ~{tokens}
...

## Appendix A: Review Decisions

### Accepted Modifications
- {item}: {original} -> {modified} - {reviewer justification}

### Discarded Items
- {item}: {reason for removal} - {reviewers who agreed}

### Added Items
- {item}: {reviewer who identified it}

## Appendix B: Severity Definitions

| Severity | Definition | Action Required |
|----------|------------|-----------------|
| CRITICAL | Causes incorrect AI behavior or dangerous outputs | Fix immediately |
| MAJOR | Causes inconsistent behavior or wasted tokens | Fix in next cycle |
| MINOR | Suboptimal but functional | Fix when convenient |
```

---

## Artifact Location

All artifacts go to:
```
claude-agency/system/cross-project-analysis/{timestamp}_prompt-analysis/
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
  - implementation-log.md (if Phase 5 executed)
```

---

## Timing

- Phase 1: Parallel (wait for all 5 analysts)
- Phase 2: Sequential (leader only)
- Phase 3: Parallel (wait for all 5 reviewers)
- Phase 4: Sequential (leader only)
- Phase 5: Parallel (wait for all) - OPTIONAL, requires user approval

---

## Analyst Feedback Template

Each Phase 1 analyst should return feedback in this format:

```markdown
# Prompt Analysis Feedback - Analyst {N}

**Documents Analyzed**: {list}
**Date**: {ISO timestamp}

## {Document Name}

### Strengths
- {what works well}

### Issues

| ID | Severity | Category | Description | Location | Suggested Fix |
|----|----------|----------|-------------|----------|---------------|
| {doc}-001 | CRITICAL | Ambiguity | {description} | {line/section} | {specific fix} |

### Clarity Assessment
- **Overall**: {HIGH | MEDIUM | LOW}
- **Implicit assumptions found**: {list}
- **Vague terms requiring definition**: {list}

### Precision Problems
| Vague Phrase | Location | Precise Alternative |
|--------------|----------|---------------------|
| "{phrase}" | {location} | "{alternative}" |

### Ambiguity Issues
- **Conflicting instructions**: {list}
- **Undefined terms**: {list}
- **Scope uncertainties**: {list}

### Missing Context
- **Missing prerequisites**: {list}
- **Missing examples**: {list}
- **Missing edge case handling**: {list}

### Structure Assessment
- **Scannability**: {HIGH | MEDIUM | LOW}
- **Prose vs structured ratio**: {description}
- **Recommendations**: {list}

### Condensation Opportunities
| Original | Condensed | Tokens Saved |
|----------|-----------|--------------|
| "{text}" | "{text}" | ~{n} |

### Cross-File Observations
- {patterns noticed that may apply to other documents}
```

---

## Related Documentation

- [Agent Analysis Pipeline](./agent-analysis-pipeline.md) - Similar structure for agent behavior analysis
- [Workflow Analysis Pipeline](./workflow-analysis-pipeline.md) - Process structure analysis
- [Prompt Engineer Agent](../../../.claude/agents/prompt-engineer.md) - Analysis criteria and standards
- [Continuous Improvement](../continuous-improvement.md) - Feedback incorporation process
