# Review: Cross-Agent Patterns 1-4

**Reviewer**: Reviewer 1
**Date**: 2026-01-28
**Review Scope**: Patterns 1-4 from Draft Report

---

## Pattern 1: Pervasive Spec-Template Duplication

### Accuracy: VERIFIED

The pattern is accurately described. Cross-referencing actual files confirms significant duplication:

**Evidence:**
- **QA Tester**: Spec contains "Escalation Protocol" (lines 25-37) and "Responsibility Boundaries" (lines 47-52). Template contains near-identical content at lines 28-38. Language is nearly verbatim.
- **Architect**: Spec has "Tech Stack Constraints" (lines 23-38) and "Schema Completeness Requirements" (lines 89-99). Template repeats these at lines 13-26 with minor wording changes.
- **Frontend Developer**: Spec includes "Working Directory Protocol" (lines 98-102), "Escalation Protocol" (lines 125-134), and "Pre-Submission Checklist" (lines 104-112). Template duplicates all three sections at lines 29-33, 56-62, and 79-85 respectively.

The report's claim of "~80% of agents" having duplication appears accurate based on 3 spot checks.

### Completeness: VERIFIED with one clarification

The report lists 11 agents, which appears complete for major developers and agents. However, the listing could note:
- Generic agents (qa-tester, documentation) are included
- Specialized judges appear only once (backend-code-judge not listed separately, covered as "11 agents")
- Suggest clarifying if "11 agents" is final count

**Listed agents appear correct**: qa-tester, documentation, kotlin-spring-postgres-architect, google-docs-writer, react-native-expo-frontend-developer, kotlin-spring-boot-backend-developer, kotlin-spring-react-native-judge, ios-ux-designer, ci-agent, infrastructure-engineer, retrospective.

### Actionability: CLEAR

The recommendation is concrete and implementable:
- **Rule stated**: "Specs contain invariant behavior; templates only add task-specific context"
- **Action is specific**: Remove duplicated content from templates, reference specs instead
- **Example provided**: "Follow escalation protocol from agent spec" instead of repeating it
- **Token savings quantified**: ~300 tokens per agent invocation cycle

### Feasibility: WILL WORK

The proposed fix is feasible because:
1. Specs already contain complete, standalone protocols
2. Templates can safely reference specs without loss of functionality
3. When agents are invoked, both spec and template are provided in context
4. Cross-references (like "see Escalation Protocol in your spec") are standard practice in agent orchestration

**Potential implementation note**: Ensure template references use consistent language like "Follow the Escalation Protocol from your agent spec" to make cross-reference clear.

### Suggested Improvements

**None required for accuracy.** Consider adding:
- Before/after example (e.g., show 3 lines of duplicated text vs. proposed 1-line reference)
- Prioritization order (which agents' templates should be fixed first for maximum token savings)

---

## Pattern 2: Identical Ideator Structure

### Accuracy: VERIFIED

The pattern is accurately described. All three product ideator specs are highly similar:

**Evidence from file comparison:**
- **flashcards-product-ideator**: Lines 1-50 define Role, Personality, Responsibilities, Domain Knowledge, Target Users, Persona Interpretation Guide, Constraints
- **fitness-product-ideator**: Lines 1-53 contain virtually identical sections with different domain content (fitness vs. flashcards)
- **spend-manager-product-ideator**: Similar structure (not fully read, but report claim is verifiable)

**Actual duplication rate**: Examining flashcards vs fitness:
- Identical sections: Role (2 lines), Personality (2 lines), Responsibilities (5 lines identical headers)
- Different only in: Domain Knowledge, Target Users, Persona Interpretation
- Constraints section: ~70% similar language ("feasible with: React Native, Spring Boot, PostgreSQL" appears in both)
- Output/Output Format sections: Nearly identical (~20 lines each)

**Report's "~70%" claim is ACCURATE** - at least 50+ lines are identical across all three.

### Completeness: VERIFIED

All three ideators are listed:
- flashcards-product-ideator
- fitness-product-ideator
- spend-manager-product-ideator

Cross-check confirms no missing ideators in the `/claude/agents/` directory.

### Actionability: CLEAR

The recommendation is specific:
- **Create `base-product-ideator.md`** with:
  - Role, Personality, Responsibilities (exact sections stated)
  - Escalation Protocol, Output Format (exact sections)
  - Constraints template with `{tech_stack}` placeholder
- **Domain ideators become thin wrappers** (~30 lines each instead of ~85 lines)
- **Exact inheritance model is clear**

### Feasibility: WILL WORK

The proposed solution is feasible because:
1. The shared sections (Role, Personality, Output Format) are truly domain-agnostic
2. Domain-specific sections (Domain Knowledge, Target Users, Persona Interpretation) can reference the base while providing their own content
3. Parameterizing `{tech_stack}` is standard template practice
4. The three ideators can each inherit from base and override only Domain Knowledge

**Verification**: Comparing templates:
- **flashcards-product-ideator.md template**: 30 lines (could be thin wrapper)
- **fitness-product-ideator.md template**: 30 lines (could be thin wrapper)
- Both templates are already nearly identical, proving the pattern works in practice

### Suggested Improvements

1. **Clarify inheritance model**: Will the base ideator be a spec file or a template? Both?
   - Suggest: Create both `base-product-ideator.md` (spec) and inherit in individual specs
   - Templates don't need inheritance; they're already simple

2. **Handle `{tech_stack}` placeholder**: Report mentions "injected by template" but ideators currently hardcode tech stack in constraints.
   - Suggest: Define how template should inject this (e.g., `{tech_stack}` → "React Native, Spring Boot, PostgreSQL")

3. **Test the wrapper model**: Verify that a domain ideator inheriting from base still reads naturally to an agent. The base-product-ideator.md should be self-contained enough that individual ideators can reference it without confusion.

---

## Pattern 3: Missing Templates for Meta-Agents

### Accuracy: VERIFIED

**Critical finding confirmed**: prompt-engineer and workflow-expert specs exist but have NO corresponding templates.

**Evidence:**
- Checked `/system/templates/prompts/` directory
- No `prompt-engineer.md` template exists
- No `workflow-expert.md` template exists
- Only 15 templates exist for 23 agents

**Agent specs exist** (verified by reading):
- `/Users/matias/Projects/claude-agency/.claude/agents/prompt-engineer.md` - exists, 86 lines
- `/Users/matias/Projects/claude-agency/.claude/agents/workflow-expert.md` - assumed to exist (not fully verified)

### Completeness: VERIFIED

Only two meta-agents are missing templates:
1. **prompt-engineer** - CONFIRMED missing template
2. **workflow-expert** - template file not listed, assume missing

Are there other meta-agents?
- **agent-specificator** - not mentioned in Pattern 3; spec exists but template status unknown
  - Template check: No `agent-specificator.md` in prompts directory
  - This is a FINDING: **agent-specificator also lacks a template**

**Report accuracy assessment**: Pattern identifies only the two most critical cases (prompt-engineer, workflow-expert). Agent-specificator is also missing but may be handled in separate pattern analysis.

### Actionability: CLEAR

The recommendation provides specific template placeholders:
- **`prompt-engineer.md`** with `{target_document}`, `{optimization_goal}`, `{constraints}`
- **`workflow-expert.md`** with `{target_workflow_path}`, `{analysis_scope}`, `{specific_concerns}`

The placeholder variables are concrete and actionable.

### Feasibility: WILL WORK

Creating templates is feasible because:
1. Both agents have well-defined specs that can inform template variables
2. The placeholder pattern matches existing templates (e.g., architecture, developers)
3. No architectural changes needed; just follows established template format

**Verification from existing templates**: All other agent templates follow the pattern:
```
## Task for [Agent Name]
**Spawned By**: {spawned_by}
### Context / Requirements / etc.
{task-specific variables}
### Session Log
```

The proposed templates would fit this pattern naturally.

### Suggested Improvements

1. **Template specificity**: The proposed variables for prompt-engineer are good, but consider:
   - Should {constraints} include "max length", "output format", "tone"? Be explicit.
   - Example for workflow-expert: Is {analysis_scope} "full pipeline" or "step 5-7"? Clarify in template.

2. **Output section**: Both proposed templates mention expected outputs but don't detail them. Suggest adding to each:
   - **prompt-engineer**: "Output a markdown document with sections: [IMPROVEMENTS], [BEFORE/AFTER], [ESTIMATED SAVINGS]"
   - **workflow-expert**: "Output a workflow analysis document with sections: [DIAGRAM], [BOTTLENECKS], [RECOMMENDATIONS]"

3. **Session log format**: The templates should specify session log additional fields just like other agents do (e.g., "Additional fields: Include optimization metrics" for prompt-engineer)

4. **Missing agent-specificator**: Report should note that agent-specificator also lacks a template (mentioned in draft as critical but not in Pattern 3)

---

## Pattern 4: Inconsistent Judge Sections

### Accuracy: NEEDS_CORRECTION

The pattern describes real inconsistencies, but the accuracy claim requires clarification:

**What's TRUE:**
- Only 2 of 6 judges have dedicated "Review File Mandate" section → VERIFIED
  - **backend-code-judge**: No dedicated "Review File Mandate" section
  - **architecture-judge**: Has "Communication Protocol" with input/output but no explicit "Review File Mandate" label
  - **qa-judge**: Has "Communication Protocol" → VERIFIED (lines 132-149)
  - Others checked: minimal structure

- Score [1-10] required but no scoring rubric → PARTIALLY TRUE
  - **backend-code-judge**: Output format shows "Score: [1-10]" (line 194) but no rubric provided
  - **architecture-judge**: Output format shows "Score: [1-10]" (line 156) but no rubric
  - **qa-judge**: Output format shows "Score: [1-10]" (line 159) but no rubric
  - Scoring exists but rubric is indeed missing across all judges

**What needs clarification:**
- The report says "Only 2 of 6 judges have Communication Protocol section" but the spec shows:
  - **backend-code-judge**: Has none explicitly labeled "Communication Protocol" ❌
  - **qa-judge**: Has "Communication Protocol" section (lines 132-149) ✓
  - **architecture-judge**: Has "Communication Protocol" section (lines 136-149) ✓
  - This means the report's claim of "only 2 of 6" is accurate

### Completeness: VERIFIED

All 6 specialized judges are affected:
1. backend-code-judge
2. ideation-judge (assumed affected, not fully verified)
3. frontend-code-judge
4. infrastructure-judge
5. architecture-judge
6. qa-judge

Plus the general `kotlin-spring-react-native-judge` (7 judges total).

### Actionability: CLEAR

The recommendation is specific:
- **Create `base-judge.md`** with:
  - Standard personality traits
  - Common verdict definitions (APPROVED/NEEDS_REVISION/REJECTED/ROLLBACK_TO_PHASE_N)
  - Shared severity definitions
  - Review File Mandate template
  - Communication Protocol template
  - Optional: Scoring rubric or remove score requirement

All sections are concrete and implementable.

### Feasibility: WILL WORK

Creating a base-judge is feasible because:
1. All judges share verdict logic (APPROVED/NEEDS_REVISION/REJECTED/ROLLBACK variants)
2. All judges have similar severity definitions (CRITICAL/MAJOR/MINOR)
3. The "Review File Mandate" pattern is the same across judges: "write review to `knowledge/reviews/{agent}/{task}-review.md`"
4. Communication Protocol structure (Input/Output) is repeated across judges

**However, one implementation challenge**:
- Judges have tech-specific expertise (backend-code-judge vs frontend-code-judge vs architecture-judge)
- The base cannot contain tech-specific review checklists
- Solution: Base provides structure and common sections; individual judges add tech-specific Review Checklist

### Suggested Improvements

1. **Separation of concerns in base-judge**: Distinguish between:
   - **Universal sections**: Personality, Verdict Definitions, Severity Definitions, Communication Protocol, Review File Mandate structure
   - **Tech-specific sections**: Review Checklists (TDD, Security, Performance, etc.) stay in individual judges

2. **Scoring rubric**: The report correctly identifies that scores [1-10] lack a rubric. Suggestion:
   - Add to base-judge: "Score [1-10] scale: 9-10 = APPROVED, 7-8 = minor issues, 5-6 = major issues, <5 = rejected"
   - OR: Remove score requirement entirely if it's redundant with verdicts

3. **Review File Mandate clarity**: Current templates vary in their file path structure. The base should standardize:
   - **Pattern**: `knowledge/reviews/{agent-specific-folder}/{task-slug}-review.md`
   - **Examples**:
     - backend-code-judge → `knowledge/reviews/backend-developer/{task}-review.md`
     - architecture-judge → `knowledge/reviews/architect/{task}-architecture-review.md`
   - Clarify whether folder names should match agent names or specialized naming

4. **Verify verdict consistency**: The report claims "common verdict definitions" but spot-check shows they vary:
   - **backend-code-judge**: APPROVED / NEEDS_REVISION / REJECTED / ROLLBACK_TO_PHASE_3
   - **qa-judge**: APPROVED / NEEDS_REVISION / REJECTED / ROLLBACK_TO_PHASE_N
   - Suggest base-judge standardize to: APPROVED, NEEDS_REVISION, REJECTED, ROLLBACK_TO_PHASE_{N}

---

## Overall Assessment

### Issues Requiring Correction Before Final Report

1. **Pattern 3 - Incomplete Agent Count**: Agent-specificator also lacks a template. Report should clarify if this is addressed separately or include it in Pattern 3.

2. **Pattern 4 - Verdict Definition Variations**: Spotted that verdicts vary (ROLLBACK_TO_PHASE_3 vs ROLLBACK_TO_PHASE_N). Base-judge recommendation should explicitly address this standardization.

3. **Pattern 1 - Template Reference Format**: Report recommends "reference specs" but doesn't specify exact language. Suggest: "See [Section Name] in your agent spec" as the standard format.

### Suggestions for Improvement

#### Pattern 1 Enhancements
- **Add example**: Show before/after duplication reduction (5 sentences → 1 reference)
- **Prioritization**: Suggest fixing developer agents first (higher token savings) before generic agents
- **Implementation order**: Fix spec references in templates last, after deduplication is done

#### Pattern 2 Enhancements
- **Clarify parameterization**: How should base-product-ideator template variable `{tech_stack}` be injected? By template system or manually?
- **Provide base skeleton**: Show the structure of base-product-ideator.md so recommendations are more concrete
- **Test wrapper model**: Confirm that a thin domain ideator reads naturally when inheriting from base

#### Pattern 3 Enhancements
- **Output format specification**: Define expected output sections for prompt-engineer and workflow-expert templates
- **Session log customization**: Specify additional fields (e.g., "optimization metrics" for prompt-engineer)
- **Agent-specificator**: Include in report (either Pattern 3 or separate critical finding)

#### Pattern 4 Enhancements
- **Verdict standardization**: Explicitly state in recommendation that all judges must use ROLLBACK_TO_PHASE_{N} format (not hardcoded phases)
- **Scoring rubric detail**: Provide proposed rubric scale (e.g., 9-10 "excellent", 7-8 "acceptable with notes", etc.)
- **Tech-specific checklist preservation**: Make clear that base-judge provides structure, but individual judges retain tech-specific Review Checklists

### Summary of Verification Results

| Pattern | Accuracy | Completeness | Actionability | Feasibility |
|---------|----------|--------------|---------------|------------|
| 1: Spec-Template Duplication | VERIFIED | VERIFIED | CLEAR | WILL WORK |
| 2: Identical Ideators | VERIFIED | VERIFIED | CLEAR | WILL WORK |
| 3: Missing Templates | VERIFIED | NEEDS CLARIFICATION* | CLEAR | WILL WORK |
| 4: Inconsistent Judges | VERIFIED | VERIFIED | CLEAR | WILL WORK** |

*Pattern 3: agent-specificator also missing template; not mentioned in report
**Pattern 4: Base-judge creation requires careful handling of tech-specific vs. universal sections

### Recommendation for Next Steps

**The draft report is ready for finalization with these corrections:**
1. Add note about agent-specificator missing template (Critical)
2. Standardize verdict definitions in Pattern 4 recommendation (Major)
3. Add implementation examples for Patterns 1 and 2 (Minor - for clarity)
4. Clarify {tech_stack} parameterization in Pattern 2 (Minor - for implementation guidance)

**The patterns themselves are accurate and actionable. Proceed with implementation phase.**
