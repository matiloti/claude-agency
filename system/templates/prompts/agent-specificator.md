## Task for Agent Specificator Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Agents to Analyze
{agents_to_analyze}

For each agent, you will analyze:
- Agent spec: `.claude/agents/{agent-name}.md`
- Prompt template: `system/templates/prompts/{agent-name}.md` (if exists)

### Output Path
{output_path}

### Analysis Constraints
{constraints}

Examples:
- "Focus only on duplication analysis"
- "Evaluate naming conventions only"
- "Full analysis per spec criteria"

### Requirements
- Apply separation criteria from your spec (spec vs template content)
- Use issue severity classification (CRITICAL/MAJOR/MINOR)
- Apply naming assessment criteria
- Check for condensation opportunities
- Follow protective constraints (never recommend removing error-preventing content)

### Expected Output
For EACH agent, produce analysis following your output format:
1. **Naming Assessment**: APPROPRIATE / NEEDS_REFINEMENT / WRONG with recommendation
2. **Strengths**: What works well (cite specific sections)
3. **Issues**: Categorized by severity with specific fixes
4. **Duplication Analysis**: Content in wrong place (spec vs template)
5. **Condensation Opportunities**: Verbose → concise transformations

Write the analysis to: `{output_path}`

### Escalation
If an agent spec is ambiguous or you cannot determine correct analysis:
- Document uncertainty under "Analysis Limitations"
- Proceed with best-effort analysis noting assumptions
- Report status as `partial` if significant uncertainty remains

### Session Log
As your last action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_agent-specificator_<short-task-slug>.md`
