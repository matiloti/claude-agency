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
- Backend: `cd {backend-folder} && ./gradlew clean build && ./gradlew test`
- Frontend: `cd {frontend-folder} && npm install && npx tsc --noEmit && npm test`

### Working Directory
- Backend commands: run from `{backend-folder}/`.
- Frontend commands: run from `{frontend-folder}/`.
- NEVER run from the project root directory.

### Expected Output
- Write build report to `knowledge/ci/build-reports/`.
- Report overall PASS/FAIL verdict.
- List any failing tests or build errors with details.
- Respond with your build report summary.

### Session Log
As your LAST action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_ci-agent_<short-task-slug>.md`
