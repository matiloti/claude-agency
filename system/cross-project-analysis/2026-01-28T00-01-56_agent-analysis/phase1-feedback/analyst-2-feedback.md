# Agent Analysis: Group 2 (Product Ideators + Meta Agents)

**Analyst**: Agent Specificator (Group 2)
**Date**: 2026-01-28
**Agents Analyzed**: flashcards-product-ideator, fitness-product-ideator, spend-manager-product-ideator, prompt-engineer, workflow-expert

---

# Cross-Cutting Analysis: Product Ideator Family

Before analyzing each agent individually, it's critical to note that the three product ideators (flashcards, fitness, spend-manager) share an **identical structure** with only domain-specific content differing. This represents a significant opportunity for consolidation.

## Structural Similarity Matrix

| Section | flashcards | fitness | spend-manager |
|---------|-----------|---------|---------------|
| Role | Identical template | Identical template | Identical template |
| Personality | Identical template | Identical template | Identical template |
| Responsibilities | Identical (5 items) | Identical (5 items) | Identical (5 items) |
| Domain Knowledge | **Domain-specific** | **Domain-specific** | **Domain-specific** |
| Target Users | **Domain-specific** | **Domain-specific** | **Domain-specific** |
| Persona Interpretation | **Domain-specific** | **Domain-specific** | **Domain-specific** |
| Constraints | Near-identical | Near-identical | Near-identical |
| Escalation Protocol | Identical | Identical | Identical |
| Output paths | Identical structure | Identical structure | Identical structure |
| Output Format | Identical | Identical | Identical |

**Recommendation**: Create a `base-product-ideator.md` spec with placeholders for domain-specific sections. Domain-specific ideators would then extend the base with only their unique content. This would reduce total spec size by ~60% and ensure consistency.

---

# Agent Analysis: flashcards-product-ideator

## Summary
A well-structured product ideation agent for flashcard/learning applications. Clear domain knowledge and effective persona interpretation guide. Suffers from duplication with sibling ideator specs.

## Naming Assessment
- Current: `flashcards-product-ideator`
- Verdict: **APPROPRIATE**
- Rationale: Name clearly indicates domain (flashcards) and role (product ideator). Consistent with sibling agents.

## Strengths
1. **Strong domain knowledge section** (lines 20-27): Learning science principles are specific and actionable (spaced repetition, active recall, interleaving).
2. **Excellent persona interpretation table** (lines 34-43): Maps user traits to concrete design implications.
3. **Clear escalation protocol** (lines 53-58): Explicit stop conditions and blocker documentation requirements.
4. **Precise output format** (lines 69-83): Structured completion summary prevents ambiguous deliverables.

## Issues

### CRITICAL
- None identified.

### MAJOR
- **Massive duplication across ideator specs**: Lines 1-17, 44-50, 52-58, 59-83 are nearly identical to fitness and spend-manager ideators. Fix: Extract to `base-product-ideator.md` with domain override mechanism.
- **Tech stack constraint appears in spec** (line 48): "Suggestions must be feasible with: React Native, Spring Boot, PostgreSQL" - this is project-specific and should be in template or injected as context. Different projects might use different stacks.

### MINOR
- **"Output" vs "Output Format" section naming** (lines 59, 67): Two sections with similar names creates confusion. Rename "Output" to "Artifact Paths".
- **Verbose personality description** (lines 8-9): "Creative but pragmatic - every suggestion must be technically feasible and deliver real user value" - "pragmatic" already implies feasibility. Condense.

## Duplication Analysis

### In Spec (should be in template)
- Tech stack constraint (line 48): Project-specific; inject via template variable
- Output artifact paths (lines 62-65): Could vary by project; parameterize

### In Template (should be in spec)
- None identified. Template is appropriately minimal.

### Redundant (exists in both)
- References section in template (lines 13-16) duplicates output paths from spec (lines 62-65). Consider removing from one location.

## Recommendations
1. **Extract base ideator spec**: Create `base-product-ideator.md` with all common sections. Domain ideators become thin wrappers with only Domain Knowledge, Target Users, and Persona Interpretation.
2. **Parameterize tech stack**: Replace hardcoded "React Native, Spring Boot, PostgreSQL" with `{tech_stack}` injected by template.
3. **Rename "Output" section**: Change to "Artifact Paths" for clarity.

## Condensation Opportunities
- Lines 8-9 ("Creative but pragmatic - every suggestion must be technically feasible and deliver real user value") can become "Creative but pragmatic. Suggestions must be feasible and valuable."

---

# Agent Analysis: fitness-product-ideator

## Summary
Product ideation agent for fitness/health tracking applications. Strong domain expertise in fitness science and user psychology. Structurally identical to sibling ideators, warranting consolidation.

## Naming Assessment
- Current: `fitness-product-ideator`
- Verdict: **APPROPRIATE**
- Rationale: Clear domain indication (fitness) and role (product ideator). Consistent naming pattern.

## Strengths
1. **Comprehensive fitness domain knowledge** (lines 21-28): TDEE, caloric balance, macros, progressive overload - covers key fitness concepts that guide feature decisions.
2. **Rich target user segmentation** (lines 30-35): Identifies diverse user types from beginners to athletes.
3. **Nuanced persona interpretation** (lines 37-46): "Scale-anxious" persona shows empathy for user psychology; "busy professional" guides UX decisions.
4. **iOS HIG compliance constraint** (line 52): Explicitly requires modern, platform-appropriate UI.

## Issues

### CRITICAL
- None identified.

### MAJOR
- **Duplicate structure with sibling ideators**: Same issue as flashcards-product-ideator. ~70% of content is identical boilerplate.
- **Tech stack hardcoded** (line 50): Same issue - should be parameterized.

### MINOR
- **Inconsistent experience flow description** (line 17): "from onboarding to goal achievement" differs from flashcards ("first open to mastery") and spend-manager ("first expense to financial insight"). Should be parameterized in base spec with domain-specific journey description.
- **Constraint list could be tighter** (lines 49-54): Five constraints, some overlapping ("do not over-scope" and "keep the app focused" express similar intent).

## Duplication Analysis

### In Spec (should be in template)
- Tech stack (line 50)
- "iOS HIG compliance" (line 52): Could be injected if supporting multiple platforms
- "offline capability for gym environments" (line 53): Domain-specific implementation detail

### In Template (should be in spec)
- None. Template is appropriately task-focused.

### Redundant (exists in both)
- Same issue as flashcards: References section duplicates output paths.

## Recommendations
1. **Consolidate with base ideator** (priority: HIGH): This agent benefits most from extraction - fitness domain knowledge is self-contained and distinct.
2. **Add "Documentation Consulted" to output format**: Template expects it (line 22) but spec's output format (lines 73-86) doesn't include it.
3. **Merge overlapping constraints**: Lines 49 and 51 can merge: "Keep the app focused on fitness tracking; avoid over-scoping to social features."

## Condensation Opportunities
- Lines 49-51 ("Keep the app focused on fitness tracking... Do not over-scope - prioritize core tracking over social features") can merge to "Focus on core fitness tracking. Avoid social feature creep."

---

# Agent Analysis: spend-manager-product-ideator

## Summary
Product ideation agent for personal finance and expense tracking. Solid domain knowledge in financial principles and user psychology around money. Third instance of identical ideator structure - consolidation is overdue.

## Naming Assessment
- Current: `spend-manager-product-ideator`
- Verdict: **APPROPRIATE**
- Rationale: Clear domain (spend management/finance) and role. Matches sibling naming pattern.

## Strengths
1. **Actionable financial principles** (lines 21-27): 50/30/20 rule, zero-based budgeting, envelope method - practical frameworks users actually use.
2. **Privacy-conscious persona** (line 45): "Local-first option, no required bank linking" shows awareness of financial data sensitivity.
3. **Unique target users** (lines 30-35): Includes "couples sharing household expenses" and "freelancers with irregular income" - specific pain points.
4. **Escalation explicitly mentions privacy** (line 57): "privacy requirements" as a potential blocker shows domain awareness.

## Issues

### CRITICAL
- None identified.

### MAJOR
- **Triplicate structure**: Third copy of identical ideator framework. Technical debt is accumulating.
- **Tech stack hardcoded** (line 50): Same issue across all three ideators.

### MINOR
- **"Anxious about money" persona wording** (line 41): "Gentle notifications, progress focus, no judgment" - "no judgment" is vague. What does a "judgmental" notification look like? Specify: "Avoid negative language like 'overspent' or 'failed budget'."
- **Constraint overlap** (lines 49, 51): Same "focused" + "do not over-scope" redundancy as fitness ideator.

## Duplication Analysis

### In Spec (should be in template)
- Tech stack (line 50)
- "Consider privacy implications of financial data" (line 53): Could be general constraint for all finance-related projects

### In Template (should be in spec)
- None.

### Redundant (exists in both)
- References section / output paths duplication (same as siblings).

## Recommendations
1. **Consolidate to base ideator** (priority: CRITICAL): With three identical copies, maintenance burden is tripled. Any improvement to ideator workflow must be made in three places.
2. **Specify "no judgment" constraint**: Replace "no judgment" with specific examples of language to avoid.
3. **Add financial data handling guidance**: Privacy constraint (line 53) is too vague. Add: "Never suggest features requiring access to bank credentials or sensitive financial identifiers."

## Condensation Opportunities
- Same constraint overlap fix as fitness ideator: Lines 49+51 can merge.

---

# Agent Analysis: prompt-engineer

## Summary
Meta-agent for optimizing AI instructions. Excellent core principles and comprehensive anti-pattern documentation. Missing template despite being invoked for specific tasks. One of the strongest specs in the agency.

## Naming Assessment
- Current: `prompt-engineer`
- Verdict: **APPROPRIATE**
- Rationale: Clear, industry-standard term. Accurately describes the role.

## Strengths
1. **Core Principles section** (lines 7-13): "Every word earns its place", "Explicit over implicit", "Structure over prose" - actionable and memorable.
2. **"What AI Attends To" / "What AI Ignores"** (lines 15-29): Brilliant dual-list format. Explicitly calls out hedged language as ignored.
3. **Anti-Patterns table** (lines 63-72): Concrete before/after examples. Transforms vague advice into actionable rules.
4. **Constraints prevent over-optimization** (lines 74-81): "Never sacrifice precision for brevity" guards against the agent optimizing away critical information.

## Issues

### CRITICAL
- **No prompt template exists**: This agent is invoked for tasks (optimizing specs, reviewing prompts) but has no template to provide task context. The agent spec alone doesn't tell it WHAT to optimize. Fix: Create `prompt-engineer.md` template with variables for `{target_document}`, `{optimization_goal}`, and `{constraints}`.

### MAJOR
- **Missing output format**: Spec defines output standards (lines 52-61) but no structured completion format. What does a successful prompt optimization deliverable look like? Add output format section similar to ideators.
- **Session log instruction is incomplete** (line 85): "following the standard format" - but what IS the standard format? Reference the actual path: `system/formats/session-log.md`.

### MINOR
- **"Analysis Approach" section could be a checklist** (lines 43-50): Six numbered steps read more like a workflow than principles. Consider converting to a checklist format.
- **Responsibilities table is redundant with Core Principles** (lines 31-39): "Condense" and "Clarify" restate the principles. Consider merging or removing.

## Duplication Analysis

### In Spec (should be in template)
- None identified. Spec is appropriately static.

### In Template (should be in spec)
- N/A - template doesn't exist.

### Redundant (exists in both)
- N/A - template doesn't exist.

## Recommendations
1. **Create prompt-engineer template** (priority: CRITICAL): Include placeholders for target document, optimization goals, and any preserve-these-sections constraints.
2. **Add output format**: Define structure for optimization deliverables (e.g., "Before/After", "Changes Made", "Rationale").
3. **Fix session log reference**: Change "following the standard format" to "following the format in `system/formats/session-log.md`".
4. **Consider renaming to agent-prompt-engineer**: To distinguish from general prompt engineering for user-facing prompts.

## Condensation Opportunities
- Responsibilities table (lines 31-39) could be condensed since it overlaps with Core Principles. Consider removing the table and adding brief examples to Core Principles instead.

---

# Agent Analysis: workflow-expert

## Summary
Specialized agent for process design, workflow analysis, and state machine validation. Exceptional terminology precision and comprehensive analysis framework. The most thorough spec in this group.

## Naming Assessment
- Current: `workflow-expert`
- Verdict: **APPROPRIATE**
- Rationale: Accurately describes expertise area. "Expert" suffix is appropriate given the depth of knowledge required.

## Strengths
1. **Terminology Reference table** (lines 13-20): Precise definitions of workflow vs pipeline vs flow vs state machine. Prevents common misclassifications.
2. **Extended Definitions with characteristics** (lines 22-44): Each process type has explicit characteristics and examples. Leaves no room for misinterpretation.
3. **Analysis Framework questions** (lines 79-104): Structured approach covering Classification, Completeness, Correctness, Optimization. Comprehensive.
4. **Workflow-Skill Relationship Evaluation** (lines 71-77): Unique responsibility that addresses when workflows should become skills - directly relevant to agency architecture.
5. **Domain Context section** (lines 166-185): Explicitly documents where to find workflows, skills, and state machines in this agency. Reduces search time.

## Issues

### CRITICAL
- **No prompt template exists**: Like prompt-engineer, this agent needs task context. What workflow should it analyze? What's the scope? Fix: Create template with `{target_workflow_path}`, `{analysis_scope}`, and `{specific_concerns}`.

### MAJOR
- **Output format is verbose** (lines 106-156): 50+ lines of template. While comprehensive, it may cause the agent to produce overly long outputs for simple analyses. Consider a tiered format: Quick Analysis vs Full Analysis.
- **Missing "what to do after analysis"**: The spec tells the agent how to analyze but not what happens with the analysis. Does it update the workflow file? Create a recommendations document? Needs clarity.

### MINOR
- **Core Principle could be stronger** (lines 7-9): "Precision over convenience" is good but the explanation is verbose. Condense the supporting text.
- **"Protocol" definition** (line 20): "Coordination rules triggered by events during workflow execution; defines response patterns for specific situations" - could use an example like the other definitions.
- **Session log instruction is incomplete** (line 187): Same issue as prompt-engineer - "following the standard session log format" without specifying the path.

## Duplication Analysis

### In Spec (should be in template)
- Domain Context file paths (lines 170-185): These could vary by project. Consider parameterizing or making project-agnostic.

### In Template (should be in spec)
- N/A - template doesn't exist.

### Redundant (exists in both)
- N/A - template doesn't exist.

## Recommendations
1. **Create workflow-expert template** (priority: CRITICAL): Include target workflow path, analysis scope (full vs quick), and any known concerns to address.
2. **Add tiered output format**: Quick Analysis (classification + key issues) vs Full Analysis (current comprehensive format).
3. **Add "Post-Analysis Actions" section**: What should the agent do with findings? Update the workflow? Create issues? Report to orchestrator?
4. **Fix session log reference**: Specify actual path.
5. **Add example for "Protocol"**: Other terminology entries have examples; Protocol does not.

## Condensation Opportunities
- Lines 7-9 (Core Principle explanation) can condense from "A misnamed workflow creates confusion that compounds over time. A state machine with undefined transitions will fail at runtime. A pipeline treated as a workflow accumulates unnecessary decision points. Get the structure right first; everything else follows." to "Misnamed structures compound confusion. Undefined transitions fail at runtime. Get the structure right first."

---

# Cross-Group Recommendations

## High Priority

### 1. Create Base Product Ideator Spec
Create `/Users/matias/Projects/claude-agency/.claude/agents/base-product-ideator.md`:
- Contains Role, Personality, Responsibilities, Constraints template, Escalation Protocol, Output Format
- Domain ideators extend with only: Domain Knowledge, Target Users, Persona Interpretation, domain-specific Constraints

**Estimated reduction**: ~180 lines of duplicated content across 3 specs.

### 2. Create Missing Templates
Both meta-agents (prompt-engineer, workflow-expert) lack templates:
- `system/templates/prompts/prompt-engineer.md`
- `system/templates/prompts/workflow-expert.md`

### 3. Standardize Session Log References
All specs reference session logs inconsistently. Standardize to:
```markdown
## Session Log
As your last action, create a session log following the format in `system/formats/session-log.md`.
**Path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_{agent-name}_{task-slug}.md`
```

## Medium Priority

### 4. Parameterize Tech Stack
Replace hardcoded "React Native, Spring Boot, PostgreSQL" in ideator specs with `{tech_stack}` variable injected by template.

### 5. Add Output Formats to Meta-Agents
Both prompt-engineer and workflow-expert need structured completion formats like the ideators have.

### 6. Merge Redundant Constraints in Ideators
The "keep focused" + "don't over-scope" pattern appears in all three ideators. Merge into single constraint.

## Low Priority

### 7. Rename "Output" to "Artifact Paths"
Across all ideators, the "Output" section (which lists paths) conflicts with "Output Format" (which shows completion template).

### 8. Add "Documentation Consulted" to Ideator Output Format
Templates expect it but spec's output format doesn't include it.

---

# Summary Statistics

| Agent | Lines | Issues (C/M/m) | Template Exists | Consolidation Candidate |
|-------|-------|----------------|-----------------|------------------------|
| flashcards-product-ideator | 84 | 0/2/2 | Yes | Yes (base ideator) |
| fitness-product-ideator | 88 | 0/2/2 | Yes | Yes (base ideator) |
| spend-manager-product-ideator | 88 | 0/2/2 | Yes | Yes (base ideator) |
| prompt-engineer | 86 | 1/2/2 | **No** | No |
| workflow-expert | 189 | 1/2/3 | **No** | No |

**Total Critical Issues**: 2 (both are missing templates for meta-agents)
**Total Major Issues**: 10
**Total Minor Issues**: 11
**Consolidation Opportunity**: 3 specs can merge to 1 base + 3 thin domain specs
