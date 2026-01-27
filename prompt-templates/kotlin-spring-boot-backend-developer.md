## Task for Backend Developer Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Implementation Request
{what to implement}

### Architecture Reference
- API spec: `knowledge/architecture/api/{resource}-api.md`
- Database schema: `knowledge/architecture/database/schema.sql`
- Migration: `knowledge/architecture/database/migrations/`
- Traceability: `knowledge/architecture/traceability-matrix.md`

### Requirements
- Follow TDD strictly: write failing test FIRST, then implement.
- Use Spring Boot JDBC (JdbcTemplate), NOT JPA/Hibernate.
- Use Testcontainers for integration tests with real PostgreSQL.
- Use Mockito for unit test mocking.
- Use JUnit 5 for all tests.
- Kotlin idiomatic code (data classes, null safety, sealed classes).
- Each commit must be small and non-breaking (TBD).
- Commit locally after each meaningful unit of work. Do NOT push until approved.
- Run `./gradlew build` and `./gradlew test` incrementally (after each endpoint/layer).
- Performance: No N+1 queries, pagination on list endpoints, proper indexes.

### Working Directory
- All commands must be run from within `flashcards-backend/`.
- All git operations must be run from within `flashcards-backend/`.
- NEVER run commands from the root `flashcards-app/` directory.

### Documentation Lookup
- Use the context7 MCP tools (`resolve-library-id` then `query-docs`) to look up current documentation for any libraries, frameworks, or tools before implementing. Always verify API usage against up-to-date docs.
- Prioritize: (1) Spring Boot JDBC/Web APIs, (2) Testcontainers setup, (3) Kotlin/Gradle configuration.
- Max 3 Context7 calls — choose the most impactful lookups.

### Troubleshooting
When facing errors or bugs, search online resources: GitHub issues, StackOverflow, Reddit. Look for similar error messages or symptoms before implementing fixes.

### Collaborative Debugging Fallback (Last Resort)
If you've made 5 failed attempts to solve the same error/bug and are still stuck:
1. STOP making further attempts yourself.
2. Spawn 5 subagents in parallel (using the Task tool with `subagent_type: "general-purpose"`), each exploring a DIFFERENT potential solution:
   - Subagent 1: Search GitHub issues for the exact error message
   - Subagent 2: Search StackOverflow/Reddit for similar symptoms
   - Subagent 3: Try an alternative library/approach to the problematic code
   - Subagent 4: Investigate if it's a version/dependency incompatibility
   - Subagent 5: Check if the issue is environmental (Docker, config, permissions)
3. Collect their findings and synthesize the most promising solution.
4. If still unresolved after this step, escalate to the orchestrator with full context.

**Important**: Only use this fallback after genuine 5 failed fix attempts. Do not invoke it prematurely.

### Escalation
If you encounter a blocker (missing spec, unclear requirement, conflicting architecture):
- STOP — do NOT proceed with assumptions.
- Document the blocker in your completion summary under "Blockers Encountered."
- Complete what you CAN without the blocked work.
- Report status as `blocked`.

### Pre-Submission
Before reporting completion:
1. Run `./gradlew build` — must pass.
2. Run `./gradlew test` — all tests must pass.
3. Complete the self-review checklist from your agent spec.
4. All work committed locally.

### Expected Output
- Implement code in `flashcards-backend/`.
- Update test reports in `knowledge/backend/test-reports/`.
- Update implementation status in `knowledge/backend/status/`.
- Create handoff file in `knowledge/handoffs/` if frontend will build on this work.
- If you notice refactoring opportunities, log them to `knowledge/backlog/tech-debt.md`.
- List "Documentation Consulted" (Context7 lookups) in your completion summary.
- Respond with your completion summary following your output format.

### Session Log
As your LAST action before completing, create a session log file at:
`sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_kotlin-spring-boot-backend-developer_<short-task-slug>.md`

Use the current date and time. The file must contain:
- **Timestamp**: Current ISO 8601 timestamp
- **Agent**: kotlin-spring-boot-backend-developer
- **Spawned By**: [value provided by orchestrator in task prompt]
- **Task**: Brief description of the task you were assigned
- **Status**: completed / blocked / needs-revision
- **Work Summary**: 2-5 sentences on what you accomplished
- **Artifacts Created/Modified**: List of files you created or changed
- **Key Decisions**: Any decisions you made
- **Blockers Encountered**: Any blockers (or "None")
- **Documentation Consulted**: Context7 lookups performed
- **Outcome**: Final result
