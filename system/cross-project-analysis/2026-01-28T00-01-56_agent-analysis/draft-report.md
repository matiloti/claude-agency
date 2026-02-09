# Agent Analysis Report - DRAFT

**Generated**: 2026-01-28T00:05:00Z
**Agents Analyzed**: 23
**Analysts**: 5 (Phase 1)
**Reviewers**: Pending (Phase 3)

---

## Executive Summary

### Overall Health
- **Total Issues Found**: 58
  - CRITICAL: 2
  - MAJOR: 38
  - MINOR: 18
- **Agents Needing Attention**: 15 of 23 (65%)
- **Cross-Cutting Patterns Identified**: 9

### Priority Actions

1. **Create missing templates** (CRITICAL): prompt-engineer and workflow-expert have no templates - cannot receive task context
2. **Resolve spec-template duplication** (HIGH): 80% of agents duplicate escalation protocol, pre-submission checklist, and working directory protocol between spec and template
3. **Create base-product-ideator spec** (HIGH): 3 ideator specs share 70% identical content - consolidation would reduce ~180 lines
4. **Standardize judge patterns** (MEDIUM): Create base-judge spec for consistent Review File Mandate, Communication Protocol, and severity definitions
5. **Rename 3 agents** (LOW): backend-code-judge → kotlin-spring-boot-code-judge, frontend-code-judge → react-native-expo-code-judge, retrospective → retrospective-analyst

---

## Cross-Agent Patterns

### Pattern 1: Pervasive Spec-Template Duplication

**Affected Agents**: qa-tester, documentation, kotlin-spring-postgres-architect, google-docs-writer, react-native-expo-frontend-developer, kotlin-spring-boot-backend-developer, kotlin-spring-react-native-judge, ios-ux-designer, ci-agent, infrastructure-engineer, retrospective (11 agents)

**Issue**: Same content appears in both spec and template, wasting tokens and creating maintenance burden:
- Escalation protocols (appears in both ~80% of agents)
- Pre-submission checklists (developers, designer)
- Working directory protocols (all developers)
- Context7 documentation lookup (developers)

**Recommendation**:
- **Rule**: Specs contain invariant behavior; templates only add task-specific context and reference specs
- Remove duplicated content from templates
- Templates should say: "Follow escalation protocol from agent spec" instead of repeating it

**Estimated Savings**: ~300 tokens per agent invocation cycle across affected agents

---

### Pattern 2: Identical Ideator Structure

**Affected Agents**: flashcards-product-ideator, fitness-product-ideator, spend-manager-product-ideator

**Issue**: 70% identical content across 3 specs. Only Domain Knowledge, Target Users, and Persona Interpretation differ.

**Recommendation**: Create `base-product-ideator.md` with:
- Role, Personality, Responsibilities
- Escalation Protocol, Output Format
- Constraints template with `{tech_stack}` placeholder

Domain ideators become thin wrappers (~30 lines each instead of ~85 lines)

**Estimated Savings**: ~180 lines total, easier maintenance

---

### Pattern 3: Missing Templates for Meta-Agents

**Affected Agents**: prompt-engineer, workflow-expert

**Issue**: These agents have specs but no templates. They cannot receive task-specific context (what to analyze, output path, constraints).

**Recommendation**: Create templates:
- `system/templates/prompts/prompt-engineer.md` with `{target_document}`, `{optimization_goal}`, `{constraints}`
- `system/templates/prompts/workflow-expert.md` with `{target_workflow_path}`, `{analysis_scope}`, `{specific_concerns}`

**Severity**: CRITICAL - agents cannot be properly invoked without templates

---

### Pattern 4: Inconsistent Judge Sections

**Affected Agents**: All 6 specialized judges + general judge

**Issue**:
- Only 2 of 6 judges have dedicated "Review File Mandate" section
- Only 2 of 6 judges have "Communication Protocol" section
- Score [1-10] required but no scoring rubric anywhere
- Skill reference formats vary

**Recommendation**: Create `base-judge.md` with:
- Standard personality traits
- Common verdict definitions (APPROVED/NEEDS_REVISION/REJECTED/ROLLBACK_TO_PHASE_N)
- Shared severity definitions
- Review File Mandate template
- Communication Protocol template
- Optional: Scoring rubric or remove score requirement

---

### Pattern 5: Hardcoded Tech Stack in Ideators

**Affected Agents**: All 3 product ideators

**Issue**: "React Native, Spring Boot, PostgreSQL" is hardcoded in constraints, making specs project-specific.

**Recommendation**: Replace with `{tech_stack}` variable injected by template. Allows reuse across projects with different stacks.

---

### Pattern 6: Inconsistent Path References

**Affected Agents**: 8+ agents

**Issue**: Mix of `../knowledge/`, `knowledge/`, absolute paths. Inconsistent navigation confuses agents.

**Recommendation**: Standardize all paths as relative from project root: `knowledge/` without `../` prefix.

---

### Pattern 7: Session Log Instructions Vary

**Affected Agents**: Most agents

**Issue**: Some say "following the standard format", others specify the path, some have complete instructions in template only.

**Recommendation**: Standardize in ALL specs:
```markdown
## Session Log
As your last action, create a session log following `system/formats/session-log.md`.
**Path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_{agent-name}_{task-slug}.md`
```

---

### Pattern 8: Generic vs Tech-Specific Naming

**Affected Agents**: backend-code-judge, frontend-code-judge

**Issue**: Names are too generic. "Backend-developer" was deemed too generic and renamed to "kotlin-spring-boot-backend-developer" - same logic applies to judges.

**Recommendation**:
- `backend-code-judge` → `kotlin-spring-boot-code-judge`
- `frontend-code-judge` → `react-native-expo-code-judge`

---

### Pattern 9: Duplicated Checklists Across Judges

**Affected Agents**: backend-code-judge, architecture-judge, infrastructure-judge, qa-judge, frontend-code-judge

**Issue**:
- Security checklists (SQL injection, auth, IDOR) appear in multiple judges
- Database patterns (indexes, N+1) duplicated
- iOS visual inspection duplicated between frontend-code-judge and qa-judge

**Recommendation**: Extract to shared files:
- `system/shared/security-checklist.md`
- `system/shared/database-checklist.md`
- Reference iOS Testing Pipeline instead of duplicating visual checklist

---

## Agent-by-Agent Analysis

### 1. qa-tester

#### Summary
Comprehensive QA agent with strong testing methodology. Contains project-specific "flashcard app" reference and significant spec-template duplication.

#### Naming
- Current: `qa-tester`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Project-specific "flashcard app" reference | spec line 5 | Remove or parameterize to `{project-name}` |
| MAJOR | Escalation protocol duplicated | spec 26-37, template 33-38 | Remove from template |
| MAJOR | Responsibility boundaries duplicated | spec 49-53, template 29-31 | Remove from template |
| MINOR | Verbose file naming section | spec lines 96-99 | Condense to pattern format |

#### Duplication Analysis
- **Move to template**: Tech-specific testing tools (spec lines 63-64)
- **Remove from template**: Escalation protocol, responsibility boundaries, output paths

#### Condensation
- File naming: 4 lines → 1 pattern line (~20 tokens saved)

---

### 2. documentation

#### Summary
Lean, well-scoped agent. Contains duplicated working guidelines between spec and template.

#### Naming
- Current: `documentation`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Working guidelines duplicated | spec 24-31, template 20-26 | Remove from template |
| MAJOR | Vague "CLAUDE.md" references | spec lines 26, 31, 44 | Make explicit or parameterize |
| MINOR | Context7 instruction missing from spec | template line 26 | Add to spec under "Tool Usage" |

---

### 3. kotlin-spring-postgres-architect

#### Summary
Thorough architect agent. Overly long (189 lines) with significant duplication.

#### Naming
- Current: `kotlin-spring-postgres-architect`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Tech stack constraints duplicated | spec 23-38, template 13-17 | Remove from template |
| MAJOR | Schema requirements duplicated | spec 89-99, template 19-26 | Remove from template |
| MAJOR | Escalation protocol duplicated | spec 76-87, template 34-38 | Remove from template |
| MAJOR | Missing Context7 reference in spec | template line 29 | Add to spec |
| MINOR | Path inconsistency | `../knowledge/` vs `knowledge/` | Standardize to `knowledge/` |

#### Condensation
- Externalize architecture templates (spec 158-189) to `system/templates/architecture-templates.md`
- Remove duplicated content from template (~50 lines saved)

---

### 4. google-docs-writer

#### Summary
Well-defined utility agent with clear formatting rules. Styling and output duplicated.

#### Naming
- Current: `google-docs-writer`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Styling requirements duplicated | spec 51-58, template 36-43 | Remove from template |
| MAJOR | Output format duplicated | spec 72-89, template 47-62 | Remove from template |
| MINOR | MCP tool names not explicit | spec lines 19-25 | Add actual MCP tool names |

---

### 5. agent-specificator

#### Summary
Meta-agent for analyzing agents. Well-structured but missing template and several sections.

#### Naming
- Current: `agent-specificator`
- Verdict: **NEEDS_REFINEMENT**
- Recommendation: `agent-spec-analyst` (clearer terminology)

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| CRITICAL | No prompt template exists | N/A | Create template with variables |
| MAJOR | Missing escalation protocol | spec | Add escalation section |
| MAJOR | Missing session log requirement | spec | Add session log section |
| MAJOR | Missing communication protocol | spec | Add Input/Output section |

---

### 6. flashcards-product-ideator

#### Summary
Well-structured ideator. ~70% identical to sibling ideators.

#### Naming
- Current: `flashcards-product-ideator`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Massive duplication with sibling ideators | lines 1-17, 44-50, 52-83 | Extract to base-product-ideator |
| MAJOR | Tech stack hardcoded | line 48 | Parameterize as `{tech_stack}` |
| MINOR | "Output" vs "Output Format" naming | lines 59, 67 | Rename "Output" to "Artifact Paths" |

---

### 7. fitness-product-ideator

#### Summary
Strong fitness domain knowledge. Duplicate structure issue.

#### Naming
- Current: `fitness-product-ideator`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Duplicate structure | ~70% of content | Extract to base ideator |
| MAJOR | Tech stack hardcoded | line 50 | Parameterize |
| MINOR | Constraint overlap | lines 49, 51 | Merge "keep focused" + "don't over-scope" |

---

### 8. spend-manager-product-ideator

#### Summary
Solid finance domain knowledge. Third instance of duplicate structure.

#### Naming
- Current: `spend-manager-product-ideator`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Triplicate structure | ~70% of content | Extract to base ideator |
| MAJOR | Tech stack hardcoded | line 50 | Parameterize |
| MINOR | "No judgment" constraint vague | line 41 | Specify language to avoid |

---

### 9. prompt-engineer

#### Summary
Excellent core principles and anti-pattern documentation. Missing template entirely.

#### Naming
- Current: `prompt-engineer`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| CRITICAL | No prompt template exists | N/A | Create template |
| MAJOR | Missing output format | spec | Add structured completion format |
| MAJOR | Incomplete session log instruction | line 85 | Add path reference |

---

### 10. workflow-expert

#### Summary
Exceptional terminology precision. Most thorough spec in group. Missing template.

#### Naming
- Current: `workflow-expert`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| CRITICAL | No prompt template exists | N/A | Create template |
| MAJOR | Verbose output format | lines 106-156 | Consider tiered format |
| MINOR | Missing "Protocol" example | line 20 | Add example like other definitions |

---

### 11-16. Specialized Judges

All 6 specialized judges share common issues:

| Judge | Naming Verdict | Key Unique Issues |
|-------|---------------|-------------------|
| backend-code-judge | NEEDS_REFINEMENT → kotlin-spring-boot-code-judge | Review path uses "backend-developer" |
| ideation-judge | APPROPRIATE | Best structured, has Communication Protocol |
| frontend-code-judge | NEEDS_REFINEMENT → react-native-expo-code-judge | Longest at 334 lines, needs condensation |
| infrastructure-judge | APPROPRIATE | Build verification unverifiable by LLM |
| architecture-judge | APPROPRIATE | Overly prescriptive (UUIDs required) |
| qa-judge | APPROPRIATE | Duplicates iOS Testing Pipeline checklist |

**Common Judge Issues**:
| Severity | Issue | Fix |
|----------|-------|-----|
| MAJOR | Inconsistent Review File Mandate sections | Add dedicated section to all judges |
| MAJOR | Inconsistent Communication Protocol sections | Add to all judges |
| MAJOR | Score [1-10] without rubric | Add rubric or remove score |
| MINOR | Skill reference format varies | Standardize to `/skill-name` |

---

### 17. kotlin-spring-react-native-judge (General)

#### Summary
Clear verdict logic with good external criteria references. Context7 verification incomplete.

#### Naming
- Current: `kotlin-spring-react-native-judge`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Context7 verification lacks severity mapping | lines 73-77 | Add severity for failed checks |
| MAJOR | Bias prevention in template should be in spec | template 13-18 | Add "Independence Protocol" to spec |
| MINOR | Personality section verbose | lines 8-12 | Condense to 1 line |

---

### 18. react-native-expo-frontend-developer

#### Summary
Comprehensive with excellent skill integration. Significant spec-template duplication.

#### Naming
- Current: `react-native-expo-frontend-developer`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Working directory protocol duplicated | spec 98-102, template 30-33 | Remove from spec |
| MAJOR | Escalation protocol duplicated | spec 125-134, template 56-62 | Remove from template |
| MAJOR | Pre-submission checklist duplicated | spec 104-112, template 79-85 | Remove from template |
| MINOR | Output format missing iOS Simulator verification | spec 157-180 | Add section |

---

### 19. kotlin-spring-boot-backend-developer

#### Summary
Solid TDD emphasis. Same duplication pattern as frontend developer.

#### Naming
- Current: `kotlin-spring-boot-backend-developer`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Working directory protocol duplicated | spec 78-82, template 30-33 | Remove from spec |
| MAJOR | Escalation protocol duplicated | spec 105-114, template 57-62 | Remove from template |
| MAJOR | Pre-submission checklist duplicated | spec 84-92, template 64-69 | Remove from template |
| MAJOR | Limited skill integration | lines 48-52 | Add more relevant skills |

---

### 20. ios-ux-designer

#### Summary
Focused iOS HIG expert with good skill integration strategy.

#### Naming
- Current: `ios-ux-designer`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Skill usage duplicated | spec 24-31, template 28-38 | Remove from template |
| MAJOR | Pre-submission duplicated | spec 61-72, template 46-53 | Remove from template |
| MAJOR | Escalation duplicated | spec 74-82, template 39-44 | Remove from template |
| MINOR | Missing Context7 integration | spec | Add for HIG docs |

---

### 21. ci-agent

#### Summary
Lightweight verification agent. Appropriately scoped.

#### Naming
- Current: `ci-agent`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Verification commands duplicated | spec 17-29, template 17-18 | Remove from spec |
| MINOR | Date format ambiguity | spec line 35 | Standardize to ISO 8601 |

---

### 22. infrastructure-engineer

#### Summary
Comprehensive but verbose (132 lines). Needs condensation.

#### Naming
- Current: `infrastructure-engineer`
- Verdict: **APPROPRIATE**

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Escalation protocol duplicated | spec 55-67, template 54-59 | Remove from template |
| MAJOR | Context7 lookup duplicated | spec 22-33, template 33-36 | Remove from template |
| MAJOR | Working directory duplicated | spec 46-53, template 27-30 | Remove from template |
| MAJOR | Collaborative Debugging in template only | template 41-52 | Move to spec |
| MINOR | Expertise list verbose | spec 8-21 | Condense to 2 lines |

---

### 23. retrospective

#### Summary
Well-designed process improvement analyst with strong CI workflow integration.

#### Naming
- Current: `retrospective`
- Verdict: **NEEDS_REFINEMENT**
- Recommendation: `retrospective-analyst` (clarifies agent vs document)

#### Issues

| Severity | Issue | Location | Fix |
|----------|-------|----------|-----|
| MAJOR | Analysis process differs between spec and template | spec 18-23, template 14-17 | Consolidate in spec |
| MAJOR | Feedback evaluation criteria in template only | template 23-29 | Move to spec |
| MINOR | Session log format reference incomplete | spec line 113 | Add path reference |

---

## Implementation Checklist

### Phase 1: Critical Fixes (Must Do)
- [ ] **prompt-engineer**: Create template with `{target_document}`, `{optimization_goal}`, `{constraints}`
- [ ] **workflow-expert**: Create template with `{target_workflow_path}`, `{analysis_scope}`, `{specific_concerns}`
- [ ] **agent-specificator**: Add escalation protocol, session log, communication protocol sections

### Phase 2: High Priority
- [ ] **All agents with templates**: Remove duplicated escalation protocols from templates
- [ ] **All ideators**: Create `base-product-ideator.md` and thin wrappers
- [ ] **All judges**: Add Review File Mandate and Communication Protocol sections
- [ ] **Developers/Designer**: Remove duplicated pre-submission checklists from templates

### Phase 3: Medium Priority
- [ ] **backend-code-judge**: Rename to `kotlin-spring-boot-code-judge`
- [ ] **frontend-code-judge**: Rename to `react-native-expo-code-judge`
- [ ] **retrospective**: Rename to `retrospective-analyst`
- [ ] **All agents**: Standardize path references to `knowledge/` (no `../`)
- [ ] **All agents**: Standardize session log instructions

### Phase 4: Low Priority
- [ ] **All judges**: Extract duplicated checklists to shared files
- [ ] **All judges**: Add scoring rubric or remove score requirement
- [ ] **kotlin-spring-postgres-architect**: Externalize architecture templates
- [ ] **All specs**: Condense verbose personality sections

---

## Appendix: Estimated Token Savings

| Category | Affected Agents | Savings per Agent | Total |
|----------|-----------------|-------------------|-------|
| Template deduplication | 11 | ~250 tokens | ~2,750 tokens |
| Base ideator extraction | 3 | ~400 tokens | ~1,200 tokens |
| Personality condensation | 15 | ~50 tokens | ~750 tokens |
| Judge consolidation | 7 | ~200 tokens | ~1,400 tokens |
| **Total Estimated Savings** | | | **~6,100 tokens** |

This represents ~15-20% reduction in total context used when invoking multiple agents.
