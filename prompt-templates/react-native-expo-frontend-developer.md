## Task for Frontend Developer Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Implementation Request
{what to implement}

### Architecture Reference
- Component plan: `knowledge/architecture/components/{component}.md`
- API spec: `knowledge/architecture/api/{resource}-api.md`
- Design system: `knowledge/frontend/design-system/tokens.md`
- Backend handoff: `knowledge/handoffs/backend-developer-to-frontend-developer-{feature}.md` (if exists)

### Requirements
- Use React Native with TypeScript.
- Style with React Native StyleSheet.
- Follow the component structure defined in your agent spec.
- Handle loading, error, and empty states.
- Write component tests.
- Each commit must be small and non-breaking (TBD).
- Commit locally after each meaningful unit of work. Do NOT push until approved.
- Run `npx tsc --noEmit` and `npm test` incrementally (after each component/screen).
- Performance: 60fps animations, virtualized lists, no unnecessary re-renders.

### Working Directory
- All commands must be run from within `flashcards-frontend/`.
- All git operations must be run from within `flashcards-frontend/`.
- NEVER run commands from the root `flashcards-app/` directory.

### Documentation Lookup
- Use the context7 MCP tools (`resolve-library-id` then `query-docs`) to look up current documentation for any libraries, frameworks, or tools before implementing. Always verify API usage against up-to-date docs.
- Prioritize: (1) React Native component APIs, (2) StyleSheet patterns and performance optimization, (3) React Navigation configuration.
- Max 3 Context7 calls — choose the most impactful lookups.

### Troubleshooting
When facing errors or bugs, search online resources: GitHub issues, StackOverflow, Reddit. Look for similar error messages or symptoms before implementing fixes.

### Escalation
If you encounter a blocker (missing API spec, unclear UX, missing design tokens):
- STOP — do NOT proceed with assumptions.
- Document the blocker in your completion summary under "Blockers Encountered."
- Complete what you CAN without the blocked work.
- Report status as `blocked`.

### Pre-Submission
Before reporting completion:
1. Run `npx tsc --noEmit` — must pass.
2. Run `npm test` — all tests must pass.
3. Complete the self-review checklist from your agent spec.
4. All work committed locally.

### Expected Output
- Implement code in `flashcards-frontend/`.
- Update component docs in `knowledge/frontend/components/`.
- Update implementation status in `knowledge/frontend/status/`.
- Update design system docs in `knowledge/frontend/design-system/` if new tokens added.
- If you notice refactoring opportunities, log them to `knowledge/backlog/tech-debt.md`.
- List "Documentation Consulted" (Context7 lookups) in your completion summary.
- Respond with your completion summary following your output format.

### Session Log
As your LAST action before completing, create a session log file at:
`sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_react-native-expo-frontend-developer_<short-task-slug>.md`

Use the current date and time. The file must contain:
- **Timestamp**: Current ISO 8601 timestamp
- **Agent**: react-native-expo-frontend-developer
- **Spawned By**: [value provided by orchestrator in task prompt]
- **Task**: Brief description of the task you were assigned
- **Status**: completed / blocked / needs-revision
- **Work Summary**: 2-5 sentences on what you accomplished
- **Artifacts Created/Modified**: List of files you created or changed
- **Key Decisions**: Any decisions you made
- **Blockers Encountered**: Any blockers (or "None")
- **Documentation Consulted**: Context7 lookups performed
- **Outcome**: Final result
