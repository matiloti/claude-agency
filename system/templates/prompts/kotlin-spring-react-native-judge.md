## Task for Judge Agent

**Spawned By**: {spawned_by}

### Review Target
- Agent: {agent name}
- Task: {original task description}
- Artifacts: {list of file paths to review}

**IMPORTANT - Bias Prevention:**
- Do NOT read any pre-summarized results or scores from the orchestrator
- Do NOT consider implied or expected verdicts
- Review ONLY the artifacts listed above against the requirements
- Form your own independent assessment

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Review Against
- Original task requirements (above).
- Agent-specific quality criteria (see your review criteria in `.claude/agents/judge.md`).
- Project standards: TDD, TBD, tech stack constraints.
- Performance standards: < 200ms API response, no N+1 queries, pagination required, proper indexes.
- Common issues from past reviews: `knowledge/reviews/common-issues.md` (if exists).

### Artifacts to Review
{list of files/artifacts produced by the agent}

### Verdict Logic
- ANY CRITICAL issue → **REJECTED**
- ANY MAJOR issue (no CRITICALs) → **NEEDS_REVISION**
- Only MINOR issues (or none) → **APPROVED**

### Review Checklist
In addition to agent-specific criteria, verify:
- [ ] Agent ran pre-submission checks (build/tests pass) — check their completion summary.
- [ ] Agent listed "Documentation Consulted" (Context7 lookups).
- [ ] Agent reported any blockers (not silently assumed).
- [ ] Performance criteria met (no N+1, pagination, indexes).
- [ ] Security review passed (see detailed checklist in your agent spec).
- [ ] Test quality criteria met (business logic tested, not just framework behavior).
- [ ] Integration contracts match (if both frontend and backend exist for this feature).

### Expected Output
- **MANDATORY**: Write review to `knowledge/reviews/{agent-name}/{task-name}-review.md`. The orchestrator will verify this file exists.
- If this issue is a recurring pattern, update `knowledge/reviews/common-issues.md`.
- Respond with your review following your output format.
- Issue clear verdict: APPROVED, NEEDS_REVISION, or REJECTED.
- If NEEDS_REVISION: provide specific, actionable issues with file/line references.
- If ROLLBACK needed: issue `ROLLBACK_TO_PHASE_{N}` with explanation.

### Session Log
As your LAST action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_kotlin-spring-react-native-judge_<short-task-slug>.md`

**Additional fields**: Include verdict rationale.
