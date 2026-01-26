## Task for Ideator Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Task
{description of what to ideate on}

### Constraints
- Keep the app simple and focused on flashcard studying.
- Target audience: students and professionals.
- Must be feasible with: React Native, Spring Boot, PostgreSQL.

### Escalation
If you encounter ambiguity that requires user input (unclear audience, conflicting needs, scope questions):
- STOP — do NOT proceed with assumptions on core product decisions.
- Document the blocker in your completion summary under "Blockers Encountered."
- Complete what you CAN without the blocked decision.
- Report status as `blocked`.

### Expected Output
- Write your output to the appropriate knowledge folder(s).
- List "Documentation Consulted" (Context7 lookups) in your completion summary.
- Respond with your completion summary following your output format.

### Documentation Lookup
- Use the context7 MCP tools (`resolve-library-id` then `query-docs`) to look up current documentation for any libraries or frameworks relevant to feasibility assessment. Always verify capabilities against up-to-date docs.
- Max 3 Context7 calls — choose the most impactful lookups.

### References
- Check `knowledge/features/priority-list.md` for existing priorities.
- Check `knowledge/personas/` for established user personas.

### Session Log
As your LAST action before completing, create a session log file at:
`sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_ideator_<short-task-slug>.md`

Use the current date and time. The file must contain:
- **Timestamp**: Current ISO 8601 timestamp
- **Agent**: ideator
- **Spawned By**: [value provided by orchestrator in task prompt]
- **Task**: Brief description of the task you were assigned
- **Status**: completed / blocked / needs-revision
- **Work Summary**: 2-5 sentences on what you accomplished
- **Artifacts Created/Modified**: List of files you created or changed
- **Key Decisions**: Any decisions you made
- **Blockers Encountered**: Any blockers (or "None")
- **Documentation Consulted**: Context7 lookups performed
- **Outcome**: Final result
