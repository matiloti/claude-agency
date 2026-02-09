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
- All commands must be run from within `{frontend-folder}/`.
- All git operations must be run from within `{frontend-folder}/`.
- NEVER run commands from the project root directory.

### Documentation Lookup
- Use the context7 MCP tools (`resolve-library-id` then `query-docs`) to look up current documentation for any libraries, frameworks, or tools before implementing. Always verify API usage against up-to-date docs.
- Prioritize: (1) React Native component APIs, (2) StyleSheet patterns and performance optimization, (3) React Navigation configuration.
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
Follow escalation protocol in agent spec.

### iOS Simulator Verification (REQUIRED)
Before reporting completion, you MUST verify your UI implementation on the iOS Simulator using the `ios-simulator` skill:

1. **Build and run** the app on the iOS Simulator (use iPhone 15 Pro or similar).
2. **Take screenshots** of the screens/components you implemented.
3. **Verify visually** that the UI matches requirements (layout, spacing, colors).
4. **Test interactions** where applicable (tap, swipe, navigation).
5. **Check accessibility** using `describe_ui` to ensure elements are properly labeled.

If the simulator build fails or UI doesn't render correctly, fix the issues before proceeding.

Include in your completion summary:
- Screenshots taken (list them)
- Visual verification status (pass/fail with notes)
- Any UI issues discovered and fixed

### Pre-Submission
Complete pre-submission checklist from agent spec. Ensure `npx tsc --noEmit` and `npm test` pass. Complete iOS Simulator verification (above).

### Expected Output
- Implement code in `{frontend-folder}/`.
- Update component docs in `knowledge/frontend/components/`.
- Update implementation status in `knowledge/frontend/status/`.
- Update design system docs in `knowledge/frontend/design-system/` if new tokens added.
- If you notice refactoring opportunities, log them to `knowledge/backlog/tech-debt.md`.
- List "Documentation Consulted" (Context7 lookups) in your completion summary.
- List "iOS Simulator Verification" results (screenshots, visual checks, interactions tested) in your completion summary.
- Respond with your completion summary following your output format.

### Session Log
As your LAST action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_react-native-expo-frontend-developer_<short-task-slug>.md`

**Additional fields for frontend**: Include iOS Simulator Verification (screenshots, visual checks, interactions tested).
