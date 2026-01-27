## Task for QA Tester Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Testing Request
{what to test}

### References
- User stories: `knowledge/user-stories/{epic}-stories.md`
- API spec: `knowledge/architecture/api/{resource}-api.md`
- Frontend status: `knowledge/frontend/status/{feature}-status.md`
- Backend status: `knowledge/backend/status/{feature}-status.md`
- Traceability: `knowledge/architecture/traceability-matrix.md`

### Scope
- Verify all acceptance criteria from user stories.
- Test happy paths and edge cases.
- Check error handling and loading states.
- Verify API contract compliance.
- Check accessibility (if frontend changes).
- Performance: API responses < 200ms, no UI lag, lists handle 100+ items.
- Regression: Run full test suite (`./gradlew test` in backend, `npm test` in frontend) to verify no regressions.

### Responsibility Boundaries
- REVIEW developer-written tests for adequacy (report gaps as issues).
- WRITE E2E test plans, test cases, bug reports in `knowledge/qa/`.
- DO NOT duplicate or rewrite developer tests.

### Escalation
If you encounter a blocker (services not running, missing test data, unclear criteria):
- STOP — do NOT test against wrong expectations.
- Document the blocker in your completion summary under "Blockers Encountered."
- Complete what you CAN without the blocked tests.
- Report status as `blocked`.

### Documentation Lookup
- Use the context7 MCP tools (`resolve-library-id` then `query-docs`) to look up current documentation for any testing libraries or frameworks before writing tests. Always verify test API usage against up-to-date docs.
- Max 3 Context7 calls — choose the most impactful lookups.

### Expected Output
- Write test plan to `knowledge/qa/test-plans/`.
- Write test cases to `knowledge/qa/test-cases/`.
- Report any bugs to `knowledge/qa/bugs/`.
- Write quality report to `knowledge/qa/reports/`.
- List "Documentation Consulted" (Context7 lookups) in your completion summary.
- Respond with your completion summary following your output format.

### Session Log
As your LAST action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_qa-tester_<short-task-slug>.md`
