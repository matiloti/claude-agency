## Task for CI Agent

**Spawned By**: {spawned_by}

### Trigger
{what triggered this run: post-commit, pre-review, regression-check}

### Scope
{which services to verify: backend, frontend, or both}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Verification Instructions
Run the appropriate build and test commands:
- Backend: `cd flashcards-backend && ./gradlew clean build && ./gradlew test`
- Frontend: `cd flashcards-frontend && npm install && npx tsc --noEmit && npm test`

### Working Directory
- Backend commands: run from `flashcards-backend/`.
- Frontend commands: run from `flashcards-frontend/`.
- NEVER run from the root `flashcards-app/` directory.

### Expected Output
- Write build report to `knowledge/ci/build-reports/`.
- Report overall PASS/FAIL verdict.
- List any failing tests or build errors with details.
- Respond with your build report summary.

### Session Log
As your LAST action before completing, create a session log file at:
`sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_ci-agent_<short-task-slug>.md`

Use the current date and time. The file must contain:
- **Timestamp**: Current ISO 8601 timestamp
- **Agent**: ci-agent
- **Spawned By**: [value provided by orchestrator in task prompt]
- **Task**: Build verification
- **Status**: completed
- **Work Summary**: Build/test results summary
- **Artifacts Created/Modified**: Build report file
- **Key Decisions**: None (automated)
- **Outcome**: PASS / FAIL with details
