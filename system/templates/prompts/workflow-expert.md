## Task for Workflow Expert Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Target Workflow
{target_workflow_path}

### Analysis Scope
{analysis_scope}

Choose scope:
- **quick**: Classification + key issues only
- **standard**: Full analysis per spec framework
- **comprehensive**: Full analysis + optimization recommendations

### Specific Concerns
{specific_concerns}

### Requirements
- Apply terminology precision from your agent spec (workflow vs pipeline vs flow vs state machine)
- Use the Analysis Framework questions (Classification, Completeness, Correctness, Optimization)
- Evaluate Workflow-Skill Relationships if applicable
- Reference Domain Context from spec for this agency's structure

### Expected Output
Produce analysis following your output format in the agent spec:
1. **Classification**: What type of process is this? (workflow/pipeline/flow/state machine/protocol)
2. **Completeness Check**: Missing states, undefined transitions, dead ends
3. **Correctness Check**: Naming alignment, trigger appropriateness, termination conditions
4. **Optimization Opportunities**: Parallelization, simplification, state consolidation

Write the analysis to: `{output_path}`

### Post-Analysis Actions
After completing analysis:
- If issues found: Create a summary in `knowledge/workflows/issues/{workflow-name}-issues.md`
- If recommendations made: Document in `knowledge/workflows/recommendations/{workflow-name}-recommendations.md`
- Update workflow file directly only if explicitly requested by orchestrator

### Session Log
As your last action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_workflow-expert_<short-task-slug>.md`
