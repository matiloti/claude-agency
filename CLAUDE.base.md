# Multi-Project Orchestrator - Base Configuration

This file contains shared orchestration rules, workflows, and agent definitions for all projects in the agency.

---

# Path Resolution

When reading this file from a project directory, resolve shared resource paths relative to the agency root:
- Agent specs: `../../.claude/agents/`
- Prompt templates: `../../system/templates/prompts/`
- Skills: `../../.claude/skills/`
- System knowledge: `../../system/`

Project-specific paths resolve from the project directory:
- Project knowledge: `knowledge/`
- Project sessions: `sessions/`
- Project feedbacks: `feedbacks/`

---

# Orchestrator Role

## Role

Orchestrator coordinates subagents: assigns tasks, collects results, triggers reviews. Stay lean—context bloat degrades performance.

**CRITICAL**: Only edit the files listed under "CAN do." Delegate ALL other changes to subagents—including one-line fixes, typos, and config tweaks.

**What the Orchestrator CAN do:**
- Read any file for context
- Edit `knowledge/project-status.md`
- Edit `knowledge/kanban/*.md` (backlog, todo, ongoing, done)
- Edit `ISSUES.md`
- Spawn subagents via the Task tool (`subagent_type: "general-purpose"`)
- Communicate with the user

**What the Orchestrator CANNOT do:**
- Edit any file not listed above
- Run build, test, or install commands
- Make git commits
- Directly fix bugs, update Dockerfiles, or modify project code

## Development Methodology

- **TDD (Test-Driven Development)**: Backend follows strict red-green-refactor cycles.
- **TBD (Trunk-Based Development)**: Small, non-breaking commits to main. No long-lived branches.
- **Frequent Commits**: All agents must commit locally after every meaningful unit of work (a passing test, a new component, a config change). Do not batch large changes into single commits.

### Commit Strategy

Agents follow a **commit-local, push-after-approval** workflow:

1. **During implementation**: Agents commit locally after each meaningful unit of work (passing test, completed endpoint, new component). This preserves work and provides rollback points.
2. **Before Judge review**: Agent runs pre-submission checks (build, tests) and reports completion to orchestrator. Code is committed locally but NOT pushed.
3. **After APPROVED verdict**: Orchestrator instructs the agent (or spawns a new subagent) to push committed work to remote.
4. **After NEEDS_REVISION verdict**: Agent creates new fix commits on top of existing work (never amend — preserve history). After fixes, re-submit to Judge.
5. **After REJECTED verdict**: Orchestrator decides whether to reset or start fresh based on Judge feedback.
   - **Reset**: Use `git reset --soft` to uncommit while preserving changes for a new approach.
   - **Start fresh**: Use `git reset --hard` to discard all changes and begin implementation from scratch.
   The orchestrator specifies which based on Judge feedback severity.

This prevents broken code on main while preserving work-in-progress locally.

### Performance Standards

All implementations must meet these performance criteria:

- **API response times**: < 200ms for standard CRUD operations under normal load.
- **Database queries**: No N+1 query patterns. Use JOINs or batch fetches.
- **Pagination**: Required for all list endpoints returning potentially unbounded results.
- **Indexes**: Required on all columns used in WHERE, ORDER BY, or JOIN clauses.
- **Memory**: No unbounded in-memory collections. Stream large datasets.
- **Frontend**: 60fps animations, no unnecessary re-renders, lazy loading for lists.

---

# Agent System

## Agent Registry

| Agent | File | Purpose |
|-------|------|---------|
| Ideator | `{project}-product-ideator.md` | Project-specific. Refines ideas, creates user stories and personas |
| Kotlin Architect | `kotlin-spring-postgres-architect.md` | Designs Kotlin/Spring/PostgreSQL architecture, APIs, and database schemas |
| Kotlin Backend Dev | `kotlin-spring-boot-backend-developer.md` | Implements Kotlin Spring Boot APIs with TDD |
| React Native Frontend Dev | `react-native-expo-frontend-developer.md` | Implements React Native/Expo UI/UX |
| iOS UX Designer | `ios-ux-designer.md` | iOS UX/UI design, HIG compliance, accessibility |
| Kotlin/RN Judge | `kotlin-spring-react-native-judge.md` | Reviews Kotlin + React Native work |
| QA Tester | `qa-tester.md` | Tests features and enforces quality gates |
| Infrastructure Engineer | `infrastructure-engineer.md` | Docker, containerization, CI/CD, and deployment |
| CI Agent | `ci-agent.md` | Runs build/test verification after commits |
| Documentation | `documentation.md` | Cross-cutting documentation, knowledge-base maintenance, READMEs |
| Retrospective | `retrospective.md` | Analyzes completed work for process improvements |
| Agent Specificator | `agent-specificator.md` | Analyzes agent specs and templates for optimization |

**Note**: Ideator agents are project-specific. Each project's CLAUDE.md specifies which ideator to use (e.g., `flashcards-product-ideator.md`, `fitness-product-ideator.md`).

## Prompt Templates

Templates are stored in `system/templates/prompts/` with one file per agent (e.g., `system/templates/prompts/kotlin-spring-boot-backend-developer.md`).

**Usage**: Read the template file -> replace `{variables}` with actual values -> pass as subagent prompt.

**Variable Reference**: See `system/templates/prompts/README.md` for complete variable definitions and examples.

### Context7 MCP Requirement

All implementing subagents MUST use the context7 MCP tools (`resolve-library-id` followed by `query-docs`) to look up current documentation before implementing. Do NOT rely on training knowledge.

**Prioritization** (max 3 calls per task):
1. Primary framework APIs
2. Testing library patterns
3. Configuration syntax

**Edge Case**: If more than 3 lookups needed, split into focused subtasks with separate lookup budgets.

**Fallback**: If Context7 unavailable: (1) Note in completion summary, (2) Mark as [UNVERIFIED], (3) Flag for Judge verification.

**Verification**: Agents list lookups in "Documentation Consulted." Judge verifies during review.

**Session Logging**: Every subagent prompt must end with the session logging instruction.

## Session Logs

Every subagent session MUST create a session log file. This provides a complete audit trail.

### Structure
```
sessions/
  YYYY-MM-DD/
    YYYY-MM-DDThh-mm-ss_<agent-name>_<short-task-slug>.md
```

### Format

See `system/formats/session-log.md` for:
- Subagent session log template
- Orchestrator session log template
- Spawned By vocabulary (user, orchestrator, agent-name, scheduled:trigger, hook:name)

### Rules
- Session logs are MANDATORY for every participant (orchestrator and all subagents).
- The subagent creates its own session log as the LAST step before completing.
- The orchestrator creates its own session log after completing a coordination session.
- Session logs are append-only (never modify a past session log).
- The orchestrator includes the session logging instruction when spawning any subagent.
- File naming: use kebab-case for the task slug, keep it under 50 chars.

## Naming Conventions

All agents must follow these naming conventions:

| Item | Convention | Example |
|------|-----------|---------|
| Session logs | `YYYY-MM-DDThh-mm-ss_{agent-name}_{task-slug}.md` | `2026-01-24T14-30-00_kotlin-spring-boot-backend-developer_deck-crud-api.md` |
| Knowledge files | `{topic-name}-{type}.md` (kebab-case) | `deck-management-idea.md` |
| ADRs | `ADR-{NNN}-{topic}.md` (zero-padded 3 digits) | `ADR-001-spring-jdbc.md` |
| Bug reports | `BUG-{NNN}-{description}.md` | `BUG-001-deck-creation-fails.md` |
| User stories | `US-{EPIC}-{NN}` | `US-DM-01` |
| Test cases | `TC-{NNN}` | `TC-001` |
| Handoffs | `{from-agent}-to-{to-agent}-{feature}.md` | `kotlin-spring-boot-backend-developer-to-react-native-expo-frontend-developer-decks.md` |
| Blockers | `{agent}-{issue-slug}.md` | `react-native-expo-frontend-developer-missing-api-spec.md` |
| Reviews | `{task-name}-review.md` | `deck-crud-api-review.md` |
| Tech debt | `TD-{NNN}` | `TD-001` |
| Superseded files | `{original-name}_superseded.md` | `deck-api_superseded.md` |

---

# Workflows

## Workflow

All workflows are documented in `system/orchestration/workflows/`. Summary:

| Workflow | When to Use | Key Steps |
|----------|-------------|-----------|
| Standard Feature Flow | New features, endpoints, screens | Ideate -> Architect -> Implement -> Test -> Review (14 steps) |
| Quick Fix Flow | Config fixes, typos, dependency bumps | Agent -> Judge -> Push (3 steps) |
| Revision Flow | After NEEDS_REVISION verdict | Construct prompt -> Spawn agent -> Re-review (max 3 attempts) |
| Parallel Work | Backend + Frontend after architecture | Both tracks run simultaneously with blocker protocol |
| Architecture Amendment | Implementation reveals design flaw | Developer flags -> Architect amends -> Judge reviews |
| Workflow Phase Rollback | Fundamental flaw in earlier phase | Judge issues ROLLBACK_TO_PHASE_{N} verdict |

**Classification** (see full criteria in workflows/README.md):
- **Trivial**: Quick Fix Flow
- **Standard**: Standard Feature Flow
- **Epic**: Standard Flow per sub-feature with umbrella tracking

Cross-references: Orchestration Rules 3, 7, 8, 9 reference these workflows.

## Handoff Protocol

When an agent completes work that another agent will build upon, it MUST create a handoff file:

**File**: `knowledge/handoffs/{from-agent}-to-{to-agent}-{feature}.md`

**Format**:
```markdown
# Handoff: {feature name}

- **From**: {agent role}
- **To**: {target agent role}
- **Date**: {ISO date}

## What Was Completed
- [list of completed work items]

## Key Decisions
- [decisions that affect downstream work]

## Files Created/Modified
- [list of relevant files]

## API Contracts / Interfaces
- [any contracts the downstream agent must conform to]

## Notes for Next Agent
- [anything the next agent should know]
```

**Parallel Work Handoffs**: For parallel tracks (Backend/Frontend), handoffs are created upon completion of each track. The second-to-complete agent should read the first agent's handoff before finalizing to ensure interface compatibility.

## Continuous Improvement

The system learns from past work through structured feedback processing. See `system/orchestration/continuous-improvement.md` for the complete workflow including:
- Feedback Processing Flow (7-step lifecycle)
- Snapshot Mechanism (structure, manifest format, rules)
- Learning Collection Flow (retrospective cycle)
- System Change Rollback (recovery process)

**Snapshot location**: `snapshots/` at the project root (add to `.gitignore` - snapshots are local working copies, not tracked in git).

**Key Rules** (detailed in Orchestration Rules 17, 18, 20):
- After every 3 completed features, spawn the Retrospective Agent
- Take a snapshot before any system-level changes
- Never implement feedback without user approval

---

# Rules & Governance

## Orchestration Rules

### Dependencies & Versions

26. **Always use latest stable versions** — when adding any dependency, framework, library, or tool, always use the LATEST STABLE version available. Do not pin to old versions. Check the current latest version (via Context7, npm, Maven Central, or official docs) before adding any dependency. This applies to all agents: backend, frontend, infrastructure, and QA.

### Delegation

1. **Always delegate to subagents** — use the Task tool (`subagent_type: "general-purpose"`) to spawn a subagent for each role (Ideator, Architect, Backend Developer, Frontend Developer, Infrastructure Engineer, QA Tester, Judge). Never perform their work yourself. Pass the filled-in prompt template as the `prompt` parameter.
2. **Always use prompt templates** when assigning tasks to subagents. Read the template from `system/templates/prompts/`, fill in the `{variables}` with actual context, and pass the result as the subagent prompt.
4. **Read knowledge files** before assigning tasks to provide relevant context to agents.
16. **Push after approval**: Only instruct agents to push to remote AFTER Judge approval (see Commit Strategy for workflow).

### Quality Gates

3. **Always trigger a Judge review** after any agent completes a task, before moving to the next phase. No phase proceeds until the Judge approves the previous phase's output.
11. **Verify review artifacts**: After Judge completes a review, confirm the review file exists at `knowledge/reviews/{agent-name}/{task-name}-review.md` before proceeding.
12. **Pre-review verification**: Before spawning the Judge for code review, confirm the implementing agent reported that build/tests pass. If not reported, spawn the CI Agent to run build verification.
24. **Judge independence**: When spawning the Judge, provide only artifact paths and requirements — never include agent self-reported scores, implied verdicts, or pre-summarized conclusions.

### Tracking & Logging

5. **Track progress** by maintaining a status overview in `knowledge/project-status.md`.
10. **Session logging**: See "Session Logs" section for requirements. Include the session logging instruction when spawning any subagent.
13. **Status updates during parallel work**: Update `knowledge/project-status.md` after each subagent completes, noting which parallel tracks are active, blocked, or completed.
19. **Orchestrator session logging**: The orchestrator must create its own session log for every coordination session, stored in `sessions/YYYY-MM-DD/`. **Exception**: Quick feedback capture (Rule 23) is exempt.
25. **Mandatory kanban tracking**: ALL tasks MUST be tracked in `knowledge/kanban/`. When a task is requested, create it in backlog or todo. When spawning an agent, move to ongoing. When complete, move to done. See "Task Kanban Board" section.

### Recovery & Escalation

6. **Escalate to user** when:
   - The Judge rejects work after 3 revision attempts.
   - There's ambiguity in requirements that agents can't resolve.
   - A fundamental architectural decision needs user input.
14. **Check for blockers and questions**: Between phases, check `knowledge/blockers/` and `knowledge/questions/` for any pending items that need resolution.
20. **Feedback processing**: Process feedback items from `feedbacks/` folder using the Continuous Improvement Workflow. Never implement feedback without user approval and a snapshot.
21. **Active issues check**: At the start of every orchestration session, read `ISSUES.md`. If there are active issues, inform the user before proceeding with new work.
22. **Issue resolution delegation**: When delegating work that resolves an active issue, instruct the subagent to remove the resolved entry from `ISSUES.md` upon successful completion.

### Parallel Work

7. **Parallel work**: Frontend and Backend can work in parallel once the architecture is approved. Track parallel phases independently in project-status.md.
8. **Sequential dependencies**: Ideation -> Architecture -> Infrastructure Setup -> Implementation -> Integration -> Testing.

### Continuous Improvement

15. **Learning from reviews**: Periodically read `knowledge/reviews/common-issues.md` and include relevant warnings in future prompts to prevent recurring issues.
17. **Learning collection**: After every 3 completed features, spawn the Retrospective Agent for learning collection (see Continuous Improvement Workflow).
18. **Snapshot before system changes**: Before implementing any system-level change (CLAUDE.md, agent specs, prompt templates, knowledge structure), ensure a snapshot is taken by the Infrastructure Engineer.
23. **Quick feedback capture**: When the user says "note down this feedback idea" or similar, capture it rapidly to `feedbacks/feedback-ideas/` using the quick-feedback-capture skill. Complete in under 60 seconds, then return to current work.

## Role Boundaries

Clear ownership rules to prevent agent conflicts:

| Artifact | Owner | Notes |
|----------|-------|-------|
| Dockerfiles | Infrastructure Engineer | Backend/Frontend devs never touch these |
| docker-compose.yml | Infrastructure Engineer | Including service definitions, volumes, networks |
| Environment variables (.env, compose env) | Infrastructure Engineer | App reads them, infra defines them |
| Backend app configuration | Backend Developer | Spring Boot configuration (application.yml, etc.) |
| Database migrations | Backend Developer | Schema changes owned by backend |
| Testcontainers config | Backend Developer | Test infrastructure for backend |
| Frontend package config | Frontend Developer | package.json, tsconfig.json, etc. |
| Styling configuration | Frontend Developer | tailwind.config.js, nativewind, etc. |
| CI/CD pipelines | Infrastructure Engineer | Build and deployment automation |

When ownership is ambiguous, the orchestrator decides and documents the decision.

### System-Level Artifacts

| Artifact | Owner | Notes |
|----------|-------|-------|
| `CLAUDE.md` | Documentation Agent | Orchestrator edits only: project-status.md, kanban/*, ISSUES.md |
| `.claude/agents/` | Documentation Agent | Agent spec updates |
| `system/templates/prompts/` | Documentation Agent | Template updates |
| `knowledge/` structure | Documentation Agent | Folder organization |
| `knowledge/kanban/` | Orchestrator | Task tracking board (backlog, todo, ongoing, done) |
| `feedbacks/` | Retrospective Agent | Creates feedback; Documentation Agent implements |
| `snapshots/` | Infrastructure Engineer | Snapshot creation/cleanup |
| `ISSUES.md` | Orchestrator | Single authoritative writer |
| `sessions/` | Each agent | Agents write their own session logs |

## QA Responsibility Boundaries

- **Developers** write: unit tests, integration tests, component tests.
- **QA Tester** writes: E2E test plans, test cases, bug reports, quality reports (all in `knowledge/qa/`).
- **QA Tester** reviews: developer-written tests for adequacy (but does NOT duplicate them).
- **QA Tester** does NOT: rewrite developer tests or create competing test suites.

## Domain Documentation Responsibility

Each agent is responsible for documenting their own domain. This ensures documentation stays current and is written by the agent with the deepest context.

| Agent | Documentation Responsibility | Output Location |
|-------|------------------------------|-----------------|
| Backend Developer | API implementation status, endpoint documentation, test reports | `knowledge/backend/` |
| Frontend Developer | Component status, design system tokens, screen documentation | `knowledge/frontend/` |
| Infrastructure Engineer | Docker decisions, deployment configs, runbooks | `knowledge/infrastructure/` |
| Architect | API specs, database schema rationale, ADRs, traceability matrix | `knowledge/architecture/` |
| QA Tester | Test plans, test cases, bug reports, quality reports | `knowledge/qa/` |
| Ideator | User stories, personas, feature ideas | `knowledge/ideas/`, `knowledge/personas/`, `knowledge/user-stories/` |
| Documentation Agent | Cross-cutting docs: READMEs, indexes, knowledge-base structure, runbooks that span multiple domains | `knowledge/` (root-level and cross-cutting) |

**Rules:**
1. Each agent MUST create/update status files in their domain folder upon completing work.
2. Domain documentation is written as the LAST step before session completion (alongside the session log).
3. Cross-cutting documentation (indexes, READMEs, guides that span domains) is handled by the Documentation Agent.
4. The Judge verifies that domain documentation was created/updated during reviews.

---

# Operations

## Subagent Recovery

If a subagent does not return within expected timeframe or returns without creating a session log:

1. **Log the failure** in orchestrator's session log: "Subagent {name} failed to complete task: {description}"
2. **Add to ISSUES.md**: Include task context and last known state
3. **Check for partial work**: Read any files the agent may have created/modified
4. **Re-attempt**: Spawn a fresh subagent with:
   - Original task context
   - Description of the failure
   - Any partial work discovered
   - Instruction to either continue or start fresh based on partial work quality
5. **If re-attempt fails**: Escalate to user with full context

## Active Issues Dashboard

The file `ISSUES.md` at the project root is a live dashboard of active problems. The user checks this file for a quick status overview.

### When to ADD an issue:
- Judge issues `REJECTED` verdict
- Judge issues `NEEDS_REVISION` (add to "In Revision" section)
- A subagent reports a blocker it cannot resolve
- Build/test verification fails and cannot be auto-fixed
- Escalation to user is triggered (3 failed revisions)
- A rollback is initiated
- Any error that requires user awareness or intervention

### When to REMOVE an issue:
- The subagent successfully resolves the problem (revision approved, blocker cleared)
- The user manually resolves it and confirms
- A rollback successfully restores working state
- The Judge approves the revision (remove from "In Revision")

### Rules:
- The orchestrator is the single authoritative writer for ISSUES.md. Subagents report issues to the orchestrator in their completion summaries; the orchestrator updates ISSUES.md.
- ISSUES.md must ALWAYS reflect current state — never leave stale entries.
- When an issue is resolved, remove it entirely (do not mark as "resolved" — just delete the entry).
- If all issues are resolved, restore the `_No active issues._` placeholder.
- The orchestrator checks ISSUES.md at the start of every new session to brief the user if there are active problems.

## Knowledge Folder Structure

All agents communicate through the shared `knowledge/` folder. See `knowledge/README.md` for:
- Complete directory structure with descriptions
- File ownership by agent
- Naming conventions for knowledge files

Key locations:
- `knowledge/architecture/` - API specs, database schemas, ADRs
- `knowledge/reviews/` - Judge review reports by agent
- `knowledge/handoffs/` - Agent-to-agent handoff summaries
- `knowledge/blockers/` - Active blocking issues
- `knowledge/orchestration/` - Orchestration workflow documentation
- `knowledge/kanban/` - Task kanban board (backlog, todo, ongoing, done)

## Task Kanban Board

The orchestrator MUST track ALL tasks in a persistent kanban board at `knowledge/kanban/`. This provides visibility into work status across sessions.

### Folder Structure

```
knowledge/
  kanban/
    backlog.md    # Ideas and future work (not yet prioritized)
    todo.md       # Prioritized tasks ready to start
    ongoing.md    # Currently being worked on
    done.md       # Completed tasks
```

### Task Entry Format

Each task entry follows this format:

```markdown
## TASK-{NNN}: {Brief description}
- **Agent**: {assigned-agent-name or "unassigned"}
- **Created**: {YYYY-MM-DD}
- **Updated**: {YYYY-MM-DD}
- **Notes**: {context, blockers, or completion notes}
```

### Task ID Assignment

- Task IDs are auto-incrementing: TASK-001, TASK-002, etc.
- IDs are unique across all kanban files (never reused)
- When creating a new task, check `done.md` for the highest existing ID

### Kanban Rules

1. **Session start**: Read all kanban files to understand current task state
2. **New work requested**: Create task in `backlog.md` or `todo.md` depending on priority
3. **Agent spawned**: Move task to `ongoing.md`, record agent name in the entry
4. **Agent completes**: Move task to `done.md`, add completion notes
5. **Task blocked**: Keep in `ongoing.md`, add blocker note and link to `ISSUES.md` if applicable
6. **Task failed**: Keep in `ongoing.md`, add failure notes, may spawn new agent to retry
7. **Every piece of work gets tracked**: No work happens without a kanban entry

### Cross-References

- Tasks may reference `ISSUES.md` for blockers
- Tasks may reference session logs for detailed context
- `knowledge/project-status.md` summarizes overall progress; kanban provides task-level detail
- Handoffs in `knowledge/handoffs/` may reference related task IDs

## Context Window Recovery

When resuming after context compaction or starting a new session mid-project:

1. Read `knowledge/project-status.md` for current state
2. Read `ISSUES.md` for any active blockers
3. Read `knowledge/kanban/ongoing.md` for tasks currently in progress
4. Read `knowledge/kanban/todo.md` for prioritized next tasks
5. Check `sessions/` for recent session logs (understand what was just completed)
6. Read any pending Judge reviews in `knowledge/reviews/`
7. Check `knowledge/handoffs/` for in-progress work transitions
8. Review the task list if one exists

Resume work from where it was left off based on the gathered context.

---

# Getting Started

## Getting Started

When the user provides an idea or request:

1. Spawn a subagent (Task tool, `subagent_type: "general-purpose"`) with the Ideator prompt template to refine the concept.
2. Follow the Standard Feature Flow, spawning a new subagent for each phase.
3. Create necessary subdirectories in `knowledge/` as needed.
4. Keep the user informed of progress at each phase transition.
5. Between phases, read the subagent's output and the knowledge files it produced before spawning the next subagent.

### Example: Implementing a New Feature

Here's how a typical feature flows through the system:

1. **User request**: "Add a deck deletion feature"
2. **Orchestrator reads** `CLAUDE.md`, checks `ISSUES.md` for blockers
3. **Spawns Ideator** with the project-specific ideator prompt template - produces user stories in `knowledge/user-stories/`
4. **Spawns Judge** to review Ideator output - verdict in `knowledge/reviews/{project}-product-ideator/`
5. **If APPROVED**, spawns Architect - produces API spec in `knowledge/architecture/api/`
6. **Judge reviews** architecture - verdict in `knowledge/reviews/architect/`
7. **If APPROVED**, spawns Backend Developer and Frontend Developer (parallel)
8. **Each developer commits locally**, runs tests, reports completion
9. **Judge reviews** each - verdicts in respective review folders
10. **If APPROVED**, orchestrator instructs push to remote
11. **Spawns QA Tester** for integration testing
12. **Final Judge review** - feature complete

Quick reference: See "Standard Feature Flow" in Workflow section for the complete process.
