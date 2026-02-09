# Prompt Analysis Report - DRAFT

**Generated**: 2026-01-27T22:55:00Z
**Document Analyzed**: CLAUDE.base.md
**Analysts**: 3 (Clarity/Precision, Ambiguity/Context, Structure/Condensation)
**Reviewers**: Pending Phase 3

---

## Executive Summary

### Overall Health
- **Critical Issues**: 5
- **Major Issues**: 18
- **Minor Issues**: 11
- **Documents Needing Attention**: 1 (CLAUDE.base.md)
- **Estimated Token Savings**: ~1,200 tokens (25%)

### Overall Clarity Score: MEDIUM

The document has strong structural organization (tables, headers, clear ownership) but suffers from three systemic problems:
1. **Undefined core mechanisms** - Task tool, Context7 MCP completely unexplained
2. **Vague operational thresholds** - "meaningful unit of work", "expected timeframe", "relevant context" lack definitions
3. **Significant duplication** - Judge reviews, session logging, push-after-approval each stated 3-4 times

### Priority Actions

1. **Add Prerequisites section** defining Task tool, Context7 MCP, and core acronyms (CRITICAL)
2. **Define operational thresholds** for "meaningful unit of work", "expected timeframe", "normal load" (CRITICAL)
3. **Consolidate duplicated rules** - session logging, Judge reviews, push-after-approval (MAJOR)
4. **Restructure Orchestrator Role** - convert 15 lines prose to single table (MAJOR)
5. **Categorize 25-rule list** into 4-5 scannable groups (MAJOR)

---

## Cross-Analyst Patterns

### Pattern: Undefined Core Mechanisms
- **Affected Sections**: Context7 MCP Requirement, Orchestration Rules, Agent System
- **Issue**: The document mandates using "Task tool" and "Context7 MCP" but never explains what these are, how to invoke them, or what responses look like. An AI following these instructions cannot execute them.
- **Unified Fix**: Add "Prerequisites and Definitions" section as first content section with: (a) Task tool syntax and parameters, (b) Context7 MCP explanation and example invocation, (c) Glossary of acronyms (TDD, TBD, ADR, CRUD, N+1, E2E)
- **Impact**: CRITICAL - Instructions are literally unexecutable without this context

### Pattern: Vague Qualifiers Without Definitions
- **Affected Sections**: Development Methodology, Orchestration Rules, Subagent Recovery, Knowledge Files
- **Issue**: Terms like "meaningful unit of work", "relevant context", "expected timeframe", "normal load" appear repeatedly but have no operational definition. Different agents could interpret these differently.
- **Unified Fix**: Create "Operational Definitions" subsection with concrete criteria:
  - "meaningful unit of work" = code that can be reverted independently without breaking other work
  - "expected timeframe" = 15 minutes for implementation, 5 minutes for review
  - "relevant context" = file paths, API specs, constraints, prior decisions
  - "normal load" = 10 concurrent users, 100 requests/minute
- **Impact**: CRITICAL - Inconsistent interpretation leads to unpredictable behavior

### Pattern: Triple/Quadruple Stated Rules
- **Affected Sections**: Session Logs, Orchestration Rules, Commit Strategy, Getting Started
- **Issue**: Same instructions appear in multiple places, wasting tokens and creating maintenance burden
- **Locations**:
  - "Judge reviews after phase": Rule 3, Rule 9, Rule 11, Getting Started example
  - "Session logging": Session Logs section, Rule 10, Rule 19, template instruction
  - "Push after approval": Rule 16, Commit Strategy lines 53-54
  - "Snapshot before changes": Rule 18, Key Rules note, Continuous Improvement section
- **Unified Fix**: Establish single canonical location for each rule, use references elsewhere
- **Impact**: MAJOR - ~200 tokens wasted, maintenance complexity

### Pattern: Prose Where Structure Would Scan Faster
- **Affected Sections**: Orchestrator Role, Commit Strategy, Getting Started, Handoff Protocol
- **Issue**: Key sections use paragraph prose that requires reading instead of tables/lists that can be scanned
- **Unified Fix**: Convert to structured formats:
  - Orchestrator Role CAN/CANNOT → single table
  - Commit Strategy → table with columns: Step | Action | Timing
  - Getting Started example → table: Phase | Agent | Output
- **Impact**: MAJOR - Reduces cognitive load, improves scannability

### Terminology Inconsistencies

| Term | Usage A | Usage B | Recommended Standard |
|------|---------|---------|---------------------|
| phase | Workflow step ("before moving to next phase") | Work state ("parallel phases") | "step" for sequence, "track" for parallel |
| task | Agent work assignment | Kanban entry (TASK-NNN) | "assignment" for agent work, "task" for kanban |
| session | Subagent lifecycle | Orchestrator period | "subagent session" vs "orchestration session" |
| meaningful | Qualitative judgment | Undefined threshold | Define with concrete criteria |

---

## Issues by Severity

### CRITICAL Issues (5)

| ID | Category | Description | Location | Fix |
|----|----------|-------------|----------|-----|
| CTX-001 | Context | Context7 MCP completely undefined - readers cannot use it | Context7 MCP Requirement | Add section explaining MCP, invocation syntax, example |
| CTX-002 | Context | Task tool undefined - core orchestration mechanism unexplained | Orchestration Rules | Add Task tool syntax and parameter reference |
| CP-001 | Precision | "relevant context" used repeatedly without definition | Lines 101, 247, 345, 467 | Define: file paths, API specs, constraints, prior decisions |
| CP-002 | Precision | "expected timeframe" for subagent completion undefined | Line 337 | Specify: "15 minutes implementation, 5 minutes review" |
| CP-003 | Precision | "meaningful unit of work" lacks operational definition | Lines 46, 52 | Define: "code that can be reverted independently" |

### MAJOR Issues (18)

| ID | Category | Description | Location | Fix |
|----|----------|-------------|----------|-----|
| CTX-003 | Ambiguity | "meaningful unit of work" has no concrete examples | Development Methodology | Add examples: passing test, complete endpoint, component |
| CTX-004 | Context | Judge verdict output format unspecified | Throughout | Specify what Judge produces and where |
| CTX-005 | Context | Subagent expected timeframe undefined | Subagent Recovery | Specify timeout value |
| CTX-006 | Context | Workflow steps referenced but not enumerated | Workflows | Inline steps or explicit file reference |
| CTX-007 | Ambiguity | "phase" vs "track" terminology inconsistent | Orchestration Rules | Standardize: "step" vs "track" |
| CTX-008 | Context | Initial file structure creation unspecified | Prerequisites | Add who creates knowledge/, sessions/, feedbacks/ |
| CP-004 | Precision | "normal load" undefined for performance standards | Line 67 | Specify: 10 users, 100 req/min |
| CP-005 | Precision | "unbounded collections" lacks threshold | Line 71 | Specify: max 10,000 items |
| CP-006 | Precision | "periodically read" common-issues has no schedule | Line 261 | Specify: session start + every 3 tasks |
| CP-007 | Precision | Quick feedback 60-second limit has no consequence | Line 268 | Add: "if exceeding, log as regular task" |
| CP-010 | Precision | Feedback severity for REJECTED undefined | Line 59 | Create severity scale |
| CP-011 | Precision | "Recent session logs" has no time boundary | Line 449 | Specify: current + previous date |
| STR-001 | Structure | 25-rule list too long for scanning | Lines 243-270 | Group into 4-5 categories |
| STR-002 | Structure | Orchestrator Role section mostly prose | Lines 22-42 | Convert to table |
| CON-001 | Condensation | Delegation explained 3 ways in 15 lines | Lines 26-41 | Single 2-line statement |
| CON-002 | Condensation | Session logging triple-explained | Lines 122-147, 255, 120 | Single section |
| DUP-001 | Duplication | Push-after-approval stated twice | Lines 261, 53-54 | Keep in Commit Strategy only |
| DUP-002 | Duplication | Judge review requirement in 4 places | Multiple | Single canonical statement |

### MINOR Issues (11)

| ID | Category | Description | Location | Fix |
|----|----------|-------------|----------|-----|
| CTX-009 | Ambiguity | TDD cycle commit timing unclear | Development Methodology | Clarify: red vs green vs refactor |
| CTX-010 | Context | ADR, E2E, CRUD acronyms undefined | Various | Add glossary or inline definitions |
| CTX-011 | Context | Parallel work failure handling unspecified | Workflows | Add edge case handling |
| CTX-012 | Ambiguity | "system-level change" boundary unclear | Rule 18 | Provide complete list |
| CP-008 | Precision | Task slug 50-char limit has no validation | Line 146 | Add truncation guidance |
| CP-009 | Precision | "max 3 calls per task" scope ambiguous | Line 109 | Clarify: per subagent or per feature |
| CP-012 | Precision | Highest ID search instruction incomplete | Line 421 | Search all kanban files |
| STR-003 | Structure | Handoff template embedded inline | Lines 196-218 | Move to separate file |
| STR-004 | Structure | Kanban entry format overstructured | Lines 409-415 | Simplify format |
| CON-003 | Condensation | Getting Started 12 verbose steps | Lines 471-486 | Convert to table |
| DUP-003 | Duplication | ISSUES.md checking duplicated | Rules 21, Recovery | Consolidate |

---

## Condensation Opportunities

### High-Impact Condensations

| Original | Condensed | Location | Tokens Saved |
|----------|-----------|----------|--------------|
| "You are the orchestrator agent. You coordinate all subagents to build this application. You break down work into tasks, assign them to the appropriate agent, collect results, and trigger reviews. You maintain the big picture and ensure all agents work cohesively toward the project goals." | "Orchestrator coordinates subagents: assigns tasks, collects results, triggers reviews." | Line 26 | ~30 |
| "**CRITICAL: You must NEVER edit any file other than `knowledge/project-status.md`.** Any change to code, config, Dockerfiles, docker-compose, documentation, knowledge-base files, READMEs, or any file in the project code directories, `knowledge/`, `system/templates/prompts/`, or `.claude/agents/` MUST be delegated to a subagent — no matter how small the change is. A one-line Dockerfile fix, a config tweak, a typo correction, a documentation update — ALL of it gets delegated." | "**CRITICAL**: Only edit `knowledge/project-status.md`. Delegate ALL other changes." | Lines 28 | ~60 |
| "The more your context window grows, the worse your performance becomes. Stay lean: your job is strictly coordination — spawning subagents with the appropriate prompt template, collecting their results, and deciding what happens next." | "Stay lean: spawn subagents, collect results, decide next steps." | Lines 28 | ~25 |
| Full Handoff Protocol template (23 lines) | Reference: `system/templates/handoff.md` | Lines 196-218 | ~150 |
| Full Kanban rules explanation (40 lines) | Table format with 4 columns | Lines 392-438 | ~100 |

### Estimated Savings

| Category | Current Tokens | After Optimization | Savings |
|----------|----------------|-------------------|---------|
| Orchestrator Role prose | ~200 | ~80 | ~120 |
| Duplicated rules | ~200 | ~50 | ~150 |
| Embedded templates | ~200 | ~20 | ~180 |
| Verbose explanations | ~400 | ~150 | ~250 |
| Filler phrases | ~100 | ~0 | ~100 |
| **Total** | **~4,800** | **~3,600** | **~1,200 (25%)** |

---

## Ambiguity Analysis

### Conflicting Instructions

| ID | Instruction A | Instruction B | Resolution |
|----|---------------|---------------|------------|
| AMB-001 | "Frequent Commits: commit after every meaningful unit" | "Before Judge review: Code committed but NOT pushed" | Clarify: continuous local commits, no push until approval |
| AMB-002 | "TDD: strict red-green-refactor cycles" | "commit after meaningful unit (passing test)" | Clarify: commit at green (passing), not red (failing) |
| AMB-003 | "Session logs MANDATORY" | "Quick feedback exempt (Rule 23)" | Rephrase: "required for all task-completing operations" |

### Undefined Terms Requiring Glossary

- **TDD**: Test-Driven Development (red=failing test, green=passing, refactor=improve)
- **TBD**: Trunk-Based Development (small commits to main, no long-lived branches)
- **ADR**: Architecture Decision Record
- **CRUD**: Create, Read, Update, Delete
- **N+1**: Query anti-pattern fetching N+1 queries instead of 1
- **E2E**: End-to-End testing
- **MCP**: Model Context Protocol (tool integration framework)

---

## Missing Context

### Prerequisites That Should Be Stated

1. **Git knowledge**: commit, push, reset --soft, reset --hard
2. **Task tool syntax**: How to invoke with `subagent_type: "general-purpose"`
3. **Context7 MCP**: What it is, how to invoke, expected responses
4. **Initial file structure**: Who creates knowledge/, sessions/, feedbacks/, kanban/

### Examples Needed

| Concept | Location | Example Needed |
|---------|----------|----------------|
| meaningful unit of work | Development Methodology | "a passing unit test", "complete endpoint with tests", "working component" |
| Context7 MCP usage | Context7 Requirement | Full invocation showing `resolve-library-id` then `query-docs` |
| Task ID assignment | Kanban Board | Show checking all files for highest ID |
| Judge review prompt | Orchestration Rules | Show "artifact paths and requirements only" example |

### Edge Cases to Address

1. Multiple agents need same file (who wins?)
2. Partial failure in parallel work (can Backend push if Frontend fails?)
3. Context7 partially available (2 of 3 lookups succeed)
4. Kanban ID collision across sessions

---

## Structure Recommendations

### Rules Section Reorganization

Current: 25 sequential rules (hard to scan)

Proposed grouping:

**Delegation Rules** (1, 2, 16)
**Quality Gates** (3, 9, 11, 12, 24)
**Tracking & Logging** (5, 10, 13, 19, 25)
**Recovery & Escalation** (6, 14, 20, 21, 22)
**Parallel Work** (7, 8)
**Continuous Improvement** (15, 17, 18, 23)

### Section Reordering

| Current Order | Recommended Order | Reason |
|---------------|-------------------|--------|
| 1. Title | 1. Title | - |
| 2. Path Resolution | 2. Prerequisites & Definitions | NEW - essential context first |
| 3. Orchestrator Role | 3. Path Resolution | After definitions |
| 4. Agent System | 4. Orchestrator Role | Core role definition |
| 5. Workflows | 5. Getting Started | Quick start early for new users |
| 6. Rules & Governance | 6. Agent System | Reference material |
| 7. Operations | 7. Workflows | How work flows |
| 8. Getting Started | 8. Rules & Governance | Rules after understanding context |
| - | 9. Operations | Operational details last |

---

## Implementation Checklist

- [ ] **Add Prerequisites section** (CTX-001, CTX-002) - Est. savings: ~0 (adds tokens but enables comprehension)
- [ ] **Add Operational Definitions** (CP-001, CP-002, CP-003) - Est. savings: ~0 (adds clarity)
- [ ] **Condense Orchestrator Role** (STR-002, CON-001) - Est. savings: ~120 tokens
- [ ] **Consolidate session logging** (CON-002, DUP-002) - Est. savings: ~100 tokens
- [ ] **Consolidate duplicated rules** (DUP-001, DUP-002, DUP-003) - Est. savings: ~150 tokens
- [ ] **Move templates to files** (STR-003) - Est. savings: ~180 tokens
- [ ] **Categorize rules list** (STR-001) - Est. savings: ~50 tokens (better scannability)
- [ ] **Add glossary** (CTX-010) - Est. savings: ~0 (adds clarity)
- [ ] **Convert verbose sections to tables** (CON-003, CON-004) - Est. savings: ~200 tokens
- [ ] **Resolve terminology inconsistencies** (CTX-007) - Est. savings: ~0 (consistency)
- [ ] **Add missing examples** (CTX-003, CTX-006) - Est. savings: ~0 (adds clarity)

**Total estimated savings**: ~800 tokens net (some additions offset by condensations)
**Total estimated clarity improvement**: HIGH

---

## Appendix: Severity Definitions

| Severity | Definition | Action Required |
|----------|------------|-----------------|
| CRITICAL | Makes instructions unexecutable or causes dangerous outputs | Fix immediately |
| MAJOR | Causes inconsistent behavior or significant token waste | Fix in next cycle |
| MINOR | Suboptimal but functional | Fix when convenient |
