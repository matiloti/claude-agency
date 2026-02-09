## Task for Prompt Engineer Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Target Document
{target_document}

### Optimization Goal
{optimization_goal}

### Constraints
{constraints}

### Analysis Scope
- Focus on: {focus_areas}
- Preserve: {sections_to_preserve}
- Maximum output length: {max_length} (if applicable)

### Requirements
- Apply core principles from your agent spec (every word earns its place, explicit over implicit, structure over prose)
- Reference the "What AI Attends To" and "What AI Ignores" lists when evaluating content
- Check against anti-patterns table before finalizing recommendations
- Do NOT sacrifice precision for brevity
- Do NOT remove constraints that prevent errors, even if verbose

### Expected Output
Produce an optimization report with:
1. **Current State Analysis**: Token count, structure assessment, identified issues
2. **Proposed Changes**: Before/after for each change with rationale
3. **Risk Assessment**: What could break if recommendations are applied incorrectly
4. **Implementation Priority**: Ordered list of changes by impact

Write the optimized document to: `{output_path}`

If the optimization requires user decision (e.g., removing a section that might be intentional), document the decision point and proceed with your best judgment, noting it in the output.

### Session Log
As your last action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_prompt-engineer_<short-task-slug>.md`
