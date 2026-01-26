## Task for Retrospective Agent

**Spawned By**: {spawned_by}

### Scope
{what to analyze: specific feature name, time period, or "all recent"}

### Context
Read the following files for analysis:
{list of session logs, reviews, and build reports to analyze}

### Analysis Focus
- Review Judge verdicts and revision counts.
- Identify recurring issues across reviews.
- Look for workflow bottlenecks (blockers, long revision cycles).
- Check for patterns in agent escalations.

### Feedback Analysis (if analyzing feedback)
Feedback file(s) to analyze:
{feedback_files}

Evaluate each feedback item for:
- Relevance to current system pain points
- Implementation effort (low/medium/high)
- Risk of regression
- Priority recommendation (critical/major/minor)
- Affected files and ownership (which agent should implement)
- Whether a snapshot is required (yes if modifying CLAUDE.md, agent specs, or prompt templates)

### Expected Output
- Write retrospective report to `knowledge/retrospectives/`.
- Update `knowledge/reviews/common-issues.md` with any new patterns found.
- If analyzing feedback: produce a prioritized recommendation with effort/risk assessment for each item.
- If generating feedback items: create structured entries in `feedbacks/` with `status.md` files.
- Respond with your analysis summary and improvement proposals.

### Session Log
As your LAST action before completing, create a session log file at:
`sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_retrospective_<short-task-slug>.md`

Use the current date and time. The file must contain:
- **Timestamp**: Current ISO 8601 timestamp
- **Agent**: retrospective
- **Spawned By**: [value provided by orchestrator in task prompt]
- **Task**: Process analysis
- **Status**: completed
- **Work Summary**: What was analyzed and key findings
- **Artifacts Created/Modified**: Retro report and common-issues updates
- **Key Decisions**: None (analysis only)
- **Outcome**: Summary of improvement proposals
