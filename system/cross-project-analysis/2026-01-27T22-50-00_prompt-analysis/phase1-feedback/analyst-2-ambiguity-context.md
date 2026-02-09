# Prompt Analysis Feedback - Analyst 2 (Ambiguity & Context)

**Document Analyzed**: CLAUDE.base.md
**Date**: 2026-01-27
**Focus**: Ambiguity Detection and Context Completeness

## Ambiguity Issues

### Conflicting Instructions

| ID | Instruction A | Instruction B | Location | Resolution |
|----|---------------|---------------|----------|------------|
| AMB-001 | "Frequent Commits: All agents must commit locally after every meaningful unit of work" | "Before Judge review: Agent runs pre-submission checks...Code is committed locally but NOT pushed" | Development Methodology vs Commit Strategy | Clarify: commits happen continuously during work, the "not pushed" rule applies to the final state before Judge review. Add explicit timing guidance. |
| AMB-002 | "TDD (Test-Driven Development): Backend follows strict red-green-refactor cycles" | "All agents must commit locally after every meaningful unit of work (a passing test, a new component)" | Development Methodology | Clarify: Does TDD require committing after each red-green-refactor cycle, or only after green? "Passing test" implies green only. |
| AMB-003 | "CLAUDE.md: Documentation Agent (Owner)" | "Orchestrator edits only knowledge/project-status.md" | Role Boundaries / System-Level Artifacts | These don't conflict but readers may wonder: Does "CLAUDE.md" here refer to project CLAUDE.md or CLAUDE.base.md? Clarify scope. |
| AMB-004 | "Session logs are MANDATORY for every participant" | "Quick feedback capture operations (Rule 23) are exempt" | Session Logs Rules / Rule 19 | While the exemption is stated, the word "MANDATORY" creates tension. Rephrase to "Session logs are required for all task-completing operations" |

### Terms Used Inconsistently

| Term | Usage 1 | Usage 2 | Sections | Recommended Standard |
|------|---------|---------|----------|---------------------|
| "phase" | A workflow step (e.g., "before moving to the next phase") | A work state in parallel tracking ("parallel phases") | Orchestration Rules 3, 7, 13 | Standardize: "workflow step" for sequential progression, "track" for parallel work streams |
| "task" | A unit of work assigned to an agent | A kanban entry (TASK-NNN) | Throughout | Clarify: "assignment" for agent work, "task" for kanban items, or use "kanban task" explicitly |
| "session" | A subagent's execution lifecycle | An orchestrator coordination period | Session Logs, Rule 19 | Add qualifiers: "subagent session" vs "orchestration session" |
| "work" | Implementation code/artifacts | Any agent activity | Rules 25, 16 | Minor but could clarify "implementation work" vs "coordination work" |

### Undefined Terms/Acronyms

- **TDD**: Defined as "Test-Driven Development" but the red-green-refactor cycle is mentioned without explanation. Readers unfamiliar with TDD won't know what "red" or "green" states mean.
- **TBD**: Defined as "Trunk-Based Development" but no explanation of what this means practically (small commits to main, no feature branches beyond short-lived).
- **ADR**: Used in naming conventions ("ADR-{NNN}") without defining what an ADR is (Architecture Decision Record).
- **Epic**: Used in "Epic" classification under Workflows without definition. Is this a large feature? Multiple user stories?
- **HIG**: Referenced in project CLAUDE.md as "HIG compliance" - Human Interface Guidelines - but not defined in base.
- **Context7 MCP**: Referenced as a requirement but never explained what MCP stands for or what Context7 is.
- **CRUD**: Used in Performance Standards without definition (Create, Read, Update, Delete).
- **N+1 query**: Referenced multiple times as something to avoid but never explained what it is.
- **E2E**: Used in QA Responsibility Boundaries without definition (End-to-End).

### Unclear Scope Boundaries

- **"meaningful unit of work"**: What constitutes "meaningful"? Is fixing a typo meaningful? Is adding one test meaningful? Need concrete examples or criteria.
- **"expected timeframe" for subagent recovery**: No timeframe is specified. How long should the orchestrator wait before considering a subagent failed?
- **"relevant context" in Rule 4**: Orchestrator should read knowledge files to "provide relevant context" - but which files are relevant? All of them? Only domain-specific ones?
- **"system-level change" in Rule 18**: Includes CLAUDE.md and agent specs, but what about workflow documentation? What about knowledge/README.md updates?
- **"Quick Fix Flow" scope**: What's the boundary between a "config fix" (Quick Fix) vs a "config change" that might need Standard Flow? Is changing a database connection string Quick Fix or Standard?

## Context Completeness

### Missing Prerequisites

- **Git knowledge assumed**: Document assumes readers understand git operations (commit, push, reset --soft, reset --hard) without verification.
- **Task tool understanding**: Rules reference "Task tool (`subagent_type: 'general-purpose'`)" without explaining what this tool is or how it works.
- **Knowledge folder must exist**: Document assumes `knowledge/`, `sessions/`, `feedbacks/` exist but doesn't specify who creates them or when.
- **ISSUES.md must exist**: Recovery and rules reference this file but don't specify initial creation or format.
- **Kanban files must exist**: Rules assume `knowledge/kanban/` structure exists with backlog.md, todo.md, etc.

### Missing Examples

| Rule/Instruction | Location | Example Needed |
|------------------|----------|----------------|
| "meaningful unit of work" | Development Methodology | List 3-4 concrete examples: "a passing unit test", "a complete API endpoint with tests", "a new component with basic rendering" |
| Context7 MCP usage | Context7 MCP Requirement | Show actual tool invocation syntax and expected response format |
| Task ID assignment | Task Kanban Board | Show how to check done.md for highest ID and create next one |
| Handoff file creation | Handoff Protocol | Complete filled-in example showing real feature handoff |
| Judge review prompt | Orchestration Rules | How to construct the Judge prompt with "only artifact paths and requirements" |
| Architecture Amendment | Workflows | When does a developer flag vs just fix themselves? |

### Undefined Inputs/Outputs

- **Standard Feature Flow "14 steps"**: Referenced but steps not enumerated. Reader must find separate workflow document.
- **Quick Fix Flow "3 steps"**: Same issue - steps referenced but not listed.
- **Revision Flow "max 3 attempts"**: What happens at attempt 3? Automatic escalation? Need explicit handoff.
- **Judge verdict format**: APPROVED, NEEDS_REVISION, REJECTED mentioned but no specification of what the Judge output file contains.
- **Subagent completion summary**: What format should agents use when reporting to orchestrator? What must be included?
- **"Documentation Consulted" format**: Agents should list lookups but format is not specified.

### Edge Cases Not Addressed

- **Multiple agents need same file**: What if Backend Developer and Infrastructure Engineer both need to modify docker-compose.yml? Who wins?
- **Judge reviews Judge**: Can Judge review its own earlier verdict if circumstances change?
- **Circular dependencies**: What if Task A blocks Task B but Task B also provides input for Task A?
- **Partial failure in parallel work**: If Backend succeeds but Frontend fails, what happens? Can Backend push? Does it wait?
- **Context7 partially available**: What if only 2 of 3 needed lookups succeed?
- **Snapshot disk space**: What happens if snapshot creation fails due to disk space?
- **Agent creates file in wrong location**: What's the recovery? Who fixes it?
- **Kanban ID collision**: What if two orchestrator sessions create TASK-042 simultaneously?
- **Session log naming collision**: Two agents with same name working on same task simultaneously (if allowed)?

## Issues Summary

| ID | Severity | Category | Description | Location | Suggested Fix |
|----|----------|----------|-------------|----------|---------------|
| CTX-001 | CRITICAL | Context | Context7 MCP completely undefined - readers cannot use it | Context7 MCP Requirement | Add section explaining what MCP is, how to invoke tools, example usage |
| CTX-002 | CRITICAL | Context | Task tool undefined - core orchestration mechanism unexplained | Orchestration Rules | Add section on Task tool syntax and parameters |
| CTX-003 | MAJOR | Ambiguity | "meaningful unit of work" undefined | Development Methodology | Add explicit examples or criteria |
| CTX-004 | MAJOR | Context | Judge verdict output format unspecified | Throughout | Add specification of what Judge produces and where |
| CTX-005 | MAJOR | Context | Subagent expected timeframe undefined | Subagent Recovery | Specify timeout (e.g., "30 minutes" or "when no progress observed") |
| CTX-006 | MAJOR | Context | Workflow steps only referenced, not enumerated | Workflows | Either inline the steps or make external document reference explicit |
| CTX-007 | MAJOR | Ambiguity | "phase" vs "track" terminology inconsistent | Orchestration Rules | Standardize terminology |
| CTX-008 | MAJOR | Context | Initial file structure creation unspecified | Prerequisites | Add "Initial Setup" section specifying who creates base structure |
| CTX-009 | MINOR | Ambiguity | TDD cycle commit timing unclear | Development Methodology | Clarify if commits happen at red, green, or refactor |
| CTX-010 | MINOR | Context | ADR, E2E, CRUD acronyms undefined | Various | Add glossary section or inline definitions |
| CTX-011 | MINOR | Context | Parallel work failure handling unspecified | Workflows | Add edge case handling for partial parallel completion |
| CTX-012 | MINOR | Ambiguity | "system-level change" boundary unclear | Rule 18 | Provide complete list of what constitutes system-level |

## Strengths

- **Clear ownership tables**: Role Boundaries section provides unambiguous artifact ownership with specific file patterns.
- **Explicit prohibition lists**: "What the Orchestrator CANNOT do" leaves no ambiguity about forbidden actions.
- **Structured naming conventions**: Complete table with examples for all artifact types reduces naming ambiguity.
- **Well-documented kanban format**: Task entry format is fully specified with all required fields.
- **Explicit escalation criteria**: Rule 6 lists exactly when to escalate to user.
- **ISSUES.md lifecycle**: Clear add/remove triggers with no ambiguity about when entries change.
- **Recovery protocol**: Subagent Recovery has clear numbered steps even if timeframe is undefined.

## Summary

The document has strong structural clarity in ownership and naming but suffers from two critical gaps: undefined core mechanisms (Context7 MCP, Task tool) and unspecified workflow details that are referenced but not enumerated. The most impactful improvements would be: (1) adding a "Prerequisites and Definitions" section covering MCP, Task tool, and acronyms; (2) standardizing "phase" vs "track" terminology; and (3) providing concrete examples for subjective terms like "meaningful unit of work." Edge cases around parallel work failures and multi-agent file conflicts also need explicit handling rules.
