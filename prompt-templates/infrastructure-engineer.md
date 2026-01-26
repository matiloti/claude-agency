## Task for Infrastructure Engineer Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Infrastructure Request
{what to fix, configure, or set up}

### Current State
- Docker Compose: `docker-compose.yml`
- Frontend Dockerfile: `flashcards-frontend/Dockerfile`
- Backend Dockerfile: `flashcards-backend/Dockerfile`
- Infrastructure status: `knowledge/infrastructure/status/`

### Constraints
- The frontend is a React Native (Expo) app targeting iOS via Expo Go on physical devices.
- The backend is Kotlin Spring Boot connecting to PostgreSQL.
- Docker containers must be accessible from the host network and LAN devices.
- Follow existing patterns in docker-compose.yml.
- You own: Dockerfiles, docker-compose.yml, .env files, CI/CD configs.
- You do NOT own: application.yml, Flyway migrations, package.json, app code.

### Working Directory
- For backend Dockerfile changes: run git operations from `flashcards-backend/`.
- For frontend Dockerfile changes: run git operations from `flashcards-frontend/`.
- For root-level files (docker-compose.yml): commit from the project root (if applicable).
- NEVER run git commands from the wrong directory.

### Documentation Lookup
- Use the context7 MCP tools (`resolve-library-id` then `query-docs`) to look up current documentation for any libraries, frameworks, or tools before implementing. Always verify API usage and configuration against up-to-date docs.
- Prioritize: (1) Docker/Compose configuration, (2) Base image best practices, (3) Service-specific setup.
- Max 3 Context7 calls — choose the most impactful lookups.

### Troubleshooting
When facing errors or bugs, search online resources: GitHub issues, StackOverflow, Reddit. Look for similar error messages or symptoms before implementing fixes.

### Escalation
If you encounter a blocker (unclear networking needs, conflicting ports, missing specs):
- STOP — do NOT proceed with assumptions.
- Document the blocker in your completion summary under "Blockers Encountered."
- Complete what you CAN without the blocked work.
- Report status as `blocked`.

### Expected Output
- Implement changes to Dockerfiles, docker-compose.yml, CI/CD configs, or other infra files.
- Commit changes from within the appropriate subdirectory (respect separate git repos). Do NOT push until approved.
- Update infrastructure status in `knowledge/infrastructure/status/`.
- Document decisions in `knowledge/infrastructure/decisions/` (if architectural).
- If you notice refactoring opportunities, log them to `knowledge/backlog/tech-debt.md`.
- List "Documentation Consulted" (Context7 lookups) in your completion summary.
- Respond with your completion summary following your output format.

### Session Log
As your LAST action before completing, create a session log file at:
`sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_infrastructure-engineer_<short-task-slug>.md`

Use the current date and time. The file must contain:
- **Timestamp**: Current ISO 8601 timestamp
- **Agent**: infrastructure-engineer
- **Spawned By**: [value provided by orchestrator in task prompt]
- **Task**: Brief description of the task you were assigned
- **Status**: completed / blocked / needs-revision
- **Work Summary**: 2-5 sentences on what you accomplished
- **Artifacts Created/Modified**: List of files you created or changed
- **Key Decisions**: Any decisions you made
- **Blockers Encountered**: Any blockers (or "None")
- **Documentation Consulted**: Context7 lookups performed
- **Outcome**: Final result
