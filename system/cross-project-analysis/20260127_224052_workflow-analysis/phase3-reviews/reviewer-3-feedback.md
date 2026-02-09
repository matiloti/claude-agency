## Review of Workflow Analysis Report (Reviewer 3)
**Focus Areas**: Workflow-Skill Mapping, Cross-Cutting Issues

---

### Items to KEEP (unchanged)

**Workflow-Skill Mapping Recommendations Section:**

- **Missing Mappings: feedback-pipeline and quick-feedback-capture**: Correct. I verified `system/orchestration/workflow-skill-mapping.md` and these two skills ARE listed in the "Skill Locations" table but are NOT present in the "Mapping Table". This is a legitimate gap.

- **Missing Mappings: Phase Rollback - "Not a skill"**: Correct rationale. User confirmation requirement genuinely breaks atomicity, making this unsuitable for skill conversion.

- **Incorrect Mappings: "None identified"**: Verified. The current mappings (Quick Fix Flow -> `/quick-fix`, Revision Flow -> `/revision`) are accurate and no mappings are incorrectly assigned.

- **Mapping Table Updates: "Add Clarification"**: The suggestion to add "This document maps orchestration workflows only. Tech-specific skills are documented separately." is valuable. Many skills in `.claude/skills/` (expo-api-routes, mobile-ios-design, supabase-postgres-best-practices, etc.) are tech-specific, not workflow implementations. Clarifying scope prevents confusion.

**Cross-Cutting Issues Section:**

- **Path Inconsistency (CRITICAL) - First two entries**: Verified. `workflow-skill-mapping.md` line 81 references `knowledge/orchestration/workflows/` but the canonical system-level location is `system/orchestration/workflows/`. Similarly, `workflows/README.md` line 142 references `knowledge/orchestration/workflow-skill-mapping.md` with incorrect relative path. These are legitimate path errors needing correction.

- **Terminology Inconsistency - "Protocol" used but not defined**: Correct. The workflow-expert terminology reference (which I reviewed) defines Workflow, Pipeline, Flow, Process, and State Machine, but not Protocol. Since parallel-work-protocol.md and phase-rollback.md both use this term, adding a definition would improve consistency.

- **State Machine Inconsistency - continuous-improvement.md lacks explicit state machine**: Verified. The file describes a "Feedback Lifecycle States" section but explicitly defers to `feedbacks/README.md`. While this is intentional deferral, the report's observation is accurate.

---

### Items to MODIFY

- **Missing Mappings: Architecture Amendment Flaw Classification**: The report suggests extracting `/classify-architecture-flaw` as a skill. While the Flaw Classification Rubric IS bounded and deterministic, it's merely a 3-row decision table (Amendment Required / Clarification Only / Developer Misunderstanding). Creating a skill for a simple lookup table adds overhead without meaningful benefit. **Modify**: Change from "Yes (extraction)" to "No - too simple for dedicated skill; keep as inline rubric". Remove from implementation checklist.

- **Path Inconsistency: Third entry - continuous-improvement.md**: The report claims `feedbacks/README.md` should reference `{project-root}/feedbacks/README.md`. However, I reviewed continuous-improvement.md and the reference to `feedbacks/README.md` appears in the context of describing where to find detailed guidance. This is an intentional relative reference within documentation prose, not a broken path. Each project has its own `feedbacks/` folder. **Modify**: Change from CRITICAL to MINOR, or remove entirely - this is not a path error but a contextual reference that varies by project.

- **Mapping Table Updates: Path references**: The report states: "UPDATE | Path references | `knowledge/orchestration/workflows/` -> `system/orchestration/workflows/`". This is correct for the system-level document, but note that `projects/flashcards-app/knowledge/orchestration/` is a VALID location for project-specific customizations. The recommendation should clarify that ONLY system-level documents need updating. **Modify**: Add qualifier - "In system-level documents only; project-level knowledge/orchestration/ paths remain valid for project-specific content."

- **Terminology Inconsistency - "Blocker" vs "Flaw"**: The report suggests standardizing on a single term. I disagree. These terms serve different semantic purposes: "Flaw" refers to architectural defects discovered during implementation; "Blocker" refers to anything preventing forward progress (dependencies, resources, technical issues). Forcing a single term conflates distinct concepts. **Modify**: Remove this recommendation or clarify that these are intentionally different concepts.

- **Terminology Inconsistency - "-Flow" suffix inconsistency**: The report notes "4 of 6 workflows" use -Flow suffix and suggests establishing a naming convention. However, accepting inconsistency is actually the correct approach here. The naming variations (standard-feature-flow, quick-fix-flow, revision-flow vs parallel-work-protocol, phase-rollback) reflect genuine semantic differences: "-flow" indicates a process flow, while "-protocol" indicates coordination rules. **Modify**: Replace recommendation with "KEEP: Naming variations reflect semantic differences between flow (process) and protocol (coordination rules)."

---

### Items to DISCARD

- **Missing Mappings: `/quick-feedback-capture` -> "N/A - standalone skill"**: This item is confusing. The report says to add this to the mapping table as having "no parent workflow" - but this is exactly what the Mapping Table already documents for workflows that don't have skills. If there's no parent workflow, it shouldn't be in the workflow-skill MAPPING at all. The Skill Locations table already includes it. **DISCARD**: Adding a "N/A - standalone skill" entry to a workflow-skill mapping table for something with no workflow is meaningless.

- **State Machine Inconsistency - pipelines/README.md lacks state machine**: The report flags that pipelines/README.md doesn't define a state machine. However, pipelines are NOT workflows - they have linear stage progression by definition, not state transitions. Pipelines don't need state machines; they need stage definitions (which the individual pipeline files provide). **DISCARD**: This is a category error - pipelines and state machines serve different purposes.

---

### Items MISSING

1. **Skill path format inconsistency**: The workflow-skill-mapping.md uses `.claude/skills/quick-fix/SKILL.md` format, but this assumes agency root. For projects using these skills, the actual path would be relative to the agency root, not the project. Consider whether skill paths should use absolute format or explicit root reference (e.g., `{agency-root}/.claude/skills/...`).

2. **Duplicate workflow files**: I observed both `system/orchestration/workflows/` AND `projects/flashcards-app/knowledge/orchestration/workflows/` contain the same workflow files (standard-feature-flow.md, quick-fix-flow.md, etc.). The relationship between system-level and project-level workflows is not documented. Are project-level workflows meant to override system-level? Are they copies that should be kept in sync? This duplication is a gap that should be noted.

3. **Missing INDEX.md reference for quick-feedback-capture**: The report mentions "quick-feedback-capture (skill): Fix INDEX.md inconsistency" but doesn't explain what the inconsistency is. This should either be elaborated or removed as unsubstantiated.

4. **State Machine Definition canonical location**: The report recommends that all process documents "define explicit state machines or reference the canonical definition in workflows/README.md" but doesn't address whether workflows/README.md IS the canonical definition. If continuous-improvement.md defers to feedbacks/README.md for its state machine, which is canonical? This needs clarification before the recommendation can be actionable.
