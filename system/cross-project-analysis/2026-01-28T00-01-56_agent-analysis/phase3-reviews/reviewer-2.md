# Review: Cross-Agent Patterns 5-9

## Pattern 5: Hardcoded Tech Stack in Ideators

**Accuracy**: VERIFIED - Issue accurately described

**Evidence**:
- `fitness-product-ideator.md` line 50: "Suggestions must be feasible with: React Native, Spring Boot, PostgreSQL"
- `flashcards-product-ideator.md` line 47: "Suggestions must be feasible with: React Native, Spring Boot, PostgreSQL"
- `spend-manager-product-ideator.md` line 50: "Suggestions must be feasible with: React Native, Spring Boot, PostgreSQL"

All three ideators contain identical tech stack constraints. This hardcoding prevents reuse for projects with different stacks (e.g., Flutter backend, different database).

**Completeness**: COMPLETE

All three product ideators are listed and verified.

**Actionability**: CLEAR

The recommendation to use `{tech_stack}` variable is concrete and implementable. The template would inject the tech stack via parameter, making the ideator specs project-agnostic.

**Suggested Improvements**:
- Consider whether tech stack should also be parameterized in the "Constraints" section title or if it should be in a separate "Technical Requirements" section for clarity.
- Define default tech stack for template invocation when not overridden.

---

## Pattern 6: Inconsistent Path References

**Accuracy**: VERIFIED - Issue accurately and comprehensively described

**Evidence**:
- `kotlin-spring-postgres-architect.md` uses `../knowledge/` prefix extensively (lines 113, 118-121, 126-131)
- `react-native-expo-frontend-developer.md` uses `knowledge/` without `../` prefix (lines 96, 143-151, 184)
- `infrastructure-engineer.md` uses `knowledge/` without `../` prefix (lines 114-121)

This inconsistency creates navigation confusion: Kotlin architect assumes parent directory navigation while frontend developer assumes project-root-relative paths.

**Completeness**: NEEDS_CLARIFICATION

The report identifies "8+ agents" but provides no comprehensive list. I spot-checked 3 agents and confirmed the issue, but a complete audit of all 23 agents wasn't provided. **Recommendation**: Verify all agents before final report.

**Actionability**: CLEAR

The recommendation to standardize on `knowledge/` (without `../`) is concrete. However, clarification needed: Is this relative to project root or relative to the `.claude/agents/` directory where specs live?

**Critical Issue Found**:
The inconsistency suggests agents operate with different directory contexts:
- Architect assumes being invoked from project root
- Frontend developer assumes being invoked from project root
- Yet architect uses `../` which would be wrong if invoked from project root

**Suggested Improvements**:
- Clarify in the recommendation whether paths should be:
  - Absolute from workspace root (e.g., `/fitness-app/knowledge/`)
  - Relative from workspace root (e.g., `knowledge/` for projects under `projects/`)
  - Project-aware using variables like `{project_root}/knowledge/`
- Add explicit instruction: "All path references MUST be testable from the invoking context"

---

## Pattern 7: Session Log Instructions Vary

**Accuracy**: VERIFIED - Issue described accurately but incompletely

**Verified**:
- The report correctly identifies that instructions vary across agents
- Some agents reference "standard format" without path details
- Others provide explicit paths

**Completeness**: INCOMPLETE

The report states "Most agents" but doesn't provide specific agent names or line references. I verified this is a real issue but cannot confirm the scope without spot-checking all 23 agents. **Key concern**: Are meta-agents (prompt-engineer, workflow-expert) also affected?

**Actionability**: CLEAR

The proposed standardized format is concrete:
```markdown
## Session Log
As your last action, create a session log following `system/formats/session-log.md`.
**Path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_{agent-name}_{task-slug}.md`
```

However, **critical question**: Does this path format work for all agents or only project-specific ones? The pattern assumes `sessions/` exists at workspace root, but project instructions (CLAUDE.md) mention project-specific `sessions/` directories.

**Suggested Improvements**:
- Verify path works for both workspace-level and project-level invocations
- Define `{task-slug}` generation rules (how to extract from task name?)
- Clarify timezone format (UTC implied by `T` but should be explicit)

---

## Pattern 8: Generic vs Tech-Specific Naming

**Accuracy**: VERIFIED - Naming consistency issue clearly articulated

**Evidence**:
- `backend-code-judge.md` is generic; should be `kotlin-spring-boot-code-judge`
- `frontend-code-judge.md` is generic; should be `react-native-expo-code-judge`
- Consistency applied successfully to developers: `kotlin-spring-boot-backend-developer`, `react-native-expo-frontend-developer`

The logic is sound: if developers are tech-specific, judges should be too.

**Completeness**: COMPLETE

Both affected judges are listed and verified.

**Actionability**: CLEAR

File renames are straightforward. However, **migration impact not addressed**:
- Are there prompt templates that reference these agent names?
- Do other agents reference these judges in escalation protocols?
- How will the rename affect running pipelines?

**Potential Issue**:
The report recommends renaming `retrospective` → `retrospective-analyst`. However, this differs from the judges' logic. "Retrospective" is a document type + agent role. The naming change seems less justified than for judges. **Recommendation**: Either justify this differently or remove it from this pattern group.

**Suggested Improvements**:
- Add migration checklist to implementation phase
- Search for all references to old judge names in specs/templates before renaming
- Consider if new judge names should be updated in any fitness-app/flashcards-app project-specific CLAUDE.md files

---

## Pattern 9: Duplicated Checklists Across Judges

**Accuracy**: PARTIALLY VERIFIED - Claims are accurate but evidence incomplete

**Verified**:
- `backend-code-judge.md` includes SQL injection checklist (lines 71-86) with vulnerable/safe examples
- `qa-judge.md` includes visual inspection checklist (lines 37-64) covering layout, colors, fonts, navigation, interactive elements, UX quality
- `frontend-code-judge.md` includes iOS HIG compliance section (lines 56-82) covering touch interactions, navigation, safe areas, system integration

**Critical Finding**: The "iOS visual inspection duplication" claim is INCORRECT
- `frontend-code-judge.md` reviews CODE compliance with iOS HIG (touch targets, navigation patterns, safe areas)
- `qa-judge.md` reviews VISUAL/RUNTIME appearance (layouts, colors, fonts, button responsiveness)
These serve different purposes and shouldn't be merged.

**Actual Duplications Confirmed**:
1. **SQL injection examples**: Appear in `backend-code-judge.md` (lines 77-86). No evidence they're duplicated elsewhere.
2. **Database patterns section**: `backend-code-judge.md` references `/supabase-postgres-best-practices` skill (line 154). Pattern identified as relevant to multiple judges but actual duplication not verified in other judges.

**Completeness**: INCOMPLETE

The report lists 5 judges as affected but provides no evidence of duplication in `architecture-judge`, `infrastructure-judge`, or `qa-judge` besides iOS inspection. Spot-check found iOS inspection is NOT actually duplicated (different concerns). **Critical flaw**: The central claim of this pattern lacks supporting evidence.

**Actionability**: PROBLEMATIC

The recommendation to extract to shared files is reasonable IF duplications exist, but the evidence doesn't support the claims as written. Extracting code-review checklists to separate files could actually REDUCE clarity since judges need self-contained review criteria.

**Suggested Improvements**:
1. **Correct the iOS visual inspection claim**: Frontend code judge reviews HIG compliance (design/code), QA judge reviews visual appearance (testing). These are complementary, not duplicate.
2. **Verify actual duplication**: Conduct full audit of all 6+ judges for truly duplicated content (security, database patterns, test quality sections)
3. **Reconsider extraction**: Some checklists are better embedded for judge autonomy. Consider:
   - Extract only when checklist is truly identical across 3+ agents
   - Keep code-review checklists embedded (judges must be self-contained)
   - Extract only reference materials (like security patterns guide)

---

## Overall Assessment

### Issues Requiring Correction Before Final Report

1. **Pattern 6 (Paths)**:
   - Incomplete agent list ("8+ agents" unspecified)
   - Unclear whether recommendation uses absolute or relative paths
   - Path context confusion not fully resolved

2. **Pattern 7 (Session Logs)**:
   - No specific agent names provided in draft
   - Path format assumes workspace-level sessions directory (conflicts with project CLAUDE.md structure)
   - `{task-slug}` derivation rules undefined

3. **Pattern 9 (Duplications)**:
   - **CRITICAL**: iOS visual inspection claim is factually incorrect (frontend-code-judge and qa-judge address different concerns)
   - Evidence for other claimed duplications not provided
   - Recommendation oversimplifies without justifying extraction vs. embedding

### Suggestions for Improvement

**Pattern 5**:
- Define how `{tech_stack}` variable is injected (template parameter, environment, etc.)
- Provide default tech stack for generic invocations

**Pattern 6**:
- Complete audit of all 23 agents for path consistency
- Clarify path resolution context (workspace root, project root, agent directory)
- Add test to verify paths work from actual invocation context

**Pattern 7**:
- Name all affected agents explicitly
- Reconcile workspace-level vs project-level session directory structure
- Define `{task-slug}` extraction algorithm

**Pattern 8**:
- Justify retrospective renaming separately or remove from this pattern
- Create migration checklist for judge renames
- Verify all references before renaming

**Pattern 9**:
- Correct the iOS visual inspection claim with evidence
- Conduct full audit of all judges for actual duplicate sections
- Distinguish between code-review checklists (embed in judge) and reference materials (extract)
- If extracting shared files, ensure judges reference them clearly and remain self-contained

### Confidence Levels

| Pattern | Confidence | Recommendation |
|---------|-----------|------------------|
| 5 | HIGH | Ready for implementation with parameter clarification |
| 6 | MEDIUM | Needs complete agent audit before implementation |
| 7 | MEDIUM | Needs scope clarification and path resolution fix |
| 8 | HIGH | Ready for implementation with migration checklist |
| 9 | LOW | Requires fact-checking; iOS claim is incorrect as stated |

---

## Reviewer Notes

This draft report demonstrates solid pattern detection methodology. The cross-agent analysis revealed genuine structural issues (duplication, inconsistency). However, Patterns 6, 7, and 9 need refinement before finalization:

- **Pattern 6** needs completion of the agent audit to be actionable
- **Pattern 7** needs clarification on path semantics given multi-level project structure
- **Pattern 9** contains a factual error about iOS visual inspection that undermines credibility

Suggest returning these three patterns to analysts for fact-checking and completion before publishing final report.
