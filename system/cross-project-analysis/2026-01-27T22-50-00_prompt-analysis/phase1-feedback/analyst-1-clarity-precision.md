# Prompt Analysis Feedback - Analyst 1 (Clarity & Precision)

**Document Analyzed**: CLAUDE.base.md
**Date**: 2026-01-27
**Focus**: Clarity and Precision Issues

## Overall Clarity Score
- **Rating**: MEDIUM
- **Justification**: The document has strong structural organization and many explicit rules. However, it contains numerous vague qualifiers, hedged language, and implicit assumptions that weaken instruction specificity. Critical thresholds are often undefined (e.g., "expected timeframe," "meaningful unit of work"), and some instructions rely on undefined concepts.

## Implicit Assumptions Found
- Assumes reader knows what "Task tool" is and how to invoke it with `subagent_type: "general-purpose"`
- Assumes reader understands what "context compaction" means
- Assumes reader knows the difference between "commit locally" and "push to remote"
- Assumes familiarity with "Context7 MCP tools" without explaining what MCP is
- Assumes understanding of "red-green-refactor cycles" (TDD terminology)
- Assumes knowledge of what constitutes "normal load" for API response time benchmarks
- Assumes reader knows how to "spawn" a subagent
- Assumes the meaning of "60fps animations" and why it matters
- Assumes understanding of N+1 query patterns
- Assumes reader knows what "CRUD operations" means
- Assumes familiarity with ISO date format requirements
- Assumes understanding of what "streaming large datasets" means in practice

## Vague Terms Requiring Definition

| Vague Phrase | Location | Precise Alternative |
|--------------|----------|---------------------|
| "meaningful unit of work" | Line 46, 52 | "a single passing test, a completed function, or a standalone config change" |
| "small, non-breaking commits" | Line 45 | "commits under 200 lines that pass all existing tests" |
| "relevant context" | Line 101, 247 | "the specific files, APIs, and constraints the agent needs to complete the task" |
| "expected timeframe" | Line 337 | "5 minutes for simple tasks, 15 minutes for complex implementations" |
| "partial work quality" | Line 345 | "code that compiles and has at least one passing test" |
| "ambiguity in requirements" | Line 250 | "when two valid interpretations exist that would lead to different implementations" |
| "fundamental architectural decision" | Line 251 | "decisions affecting database schema, API versioning, or cross-service boundaries" |
| "appropriate agent" | Line 26 | Specify the exact agent selection criteria |
| "Quick Fix Flow" | Line 177 | Define what qualifies as "trivial" vs "standard" |
| "large datasets" | Line 71 | "collections exceeding 1000 items or 10MB" |

## Precision Problems

| ID | Severity | Description | Location | Suggested Fix |
|----|----------|-------------|----------|---------------|
| CP-001 | CRITICAL | "relevant context" is used repeatedly but never defined | Lines 101, 247, 345, 467 | Create a section defining what context must be provided: file paths, API specs, constraints, prior decisions |
| CP-002 | CRITICAL | "expected timeframe" for subagent completion is undefined | Line 337 | Specify concrete timeouts: "10 minutes for implementation, 5 minutes for review, 2 minutes for documentation" |
| CP-003 | CRITICAL | "meaningful unit of work" lacks operational definition | Lines 46, 52 | Define as: "code that can be reverted independently without breaking other committed work" |
| CP-004 | MAJOR | Performance standard "normal load" is undefined | Line 67 | Specify: "10 concurrent users, 100 requests per minute" |
| CP-005 | MAJOR | "No unbounded in-memory collections" lacks threshold | Line 71 | Specify maximum: "collections must be bounded to 10,000 items maximum" |
| CP-006 | MAJOR | "periodically read" common-issues.md has no schedule | Line 261 | Specify: "at the start of each session and after every 3 task completions" |
| CP-007 | MAJOR | "under 60 seconds" for quick feedback but no consequence for exceeding | Line 268 | Add: "if exceeding 60 seconds, log as regular task instead" |
| CP-008 | MINOR | "keep it under 50 chars" for task slug but no validation | Line 146 | Add: "truncate or abbreviate if exceeding limit" |
| CP-009 | MINOR | "max 3 calls per task" for Context7 but "task" is ambiguous | Line 109 | Define: "per subagent invocation" or "per feature implementation" |
| CP-010 | MAJOR | "feedback severity" for REJECTED verdict handling is undefined | Line 59 | Create severity scale: "MINOR: fixable in current approach, MAJOR: requires new approach, CRITICAL: architectural flaw" |
| CP-011 | MAJOR | "Recent session logs" in recovery has no time boundary | Line 449 | Specify: "session logs from the current date and previous date" |
| CP-012 | MINOR | "Highest existing ID" search in done.md may miss IDs in other files | Line 421 | Search all kanban files for highest ID, not just done.md |

## Hedged Language

| Current Phrasing | Location | Direct Alternative |
|------------------|----------|-------------------|
| "should know" | Line 217 | "must know" |
| "may reference" | Lines 435-438 | "must reference when applicable" |
| "depending on priority" | Line 426 | Define priority criteria explicitly |
| "as needed" | Line 465 | "when the agent requires a location that does not exist" |
| "If applicable" | (implicit in many rules) | Remove hedge; either apply the rule or state the exception |
| "based on partial work quality" | Line 345 | "continue if code compiles; start fresh otherwise" |
| "based on Judge feedback severity" | Line 59 | Define severity levels with concrete thresholds |
| "may spawn new agent" | Line 430 | "must spawn new agent after 2 failures" |

## Strengths
- Clear table format for agent registry, role boundaries, and naming conventions
- Explicit "CAN do" and "CANNOT do" lists for orchestrator
- Well-defined handoff protocol template with required fields
- Numbered rules provide unambiguous reference points
- Session log structure is precisely specified with file naming convention
- Kanban task entry format is explicit and complete
- Performance standards section attempts to quantify expectations
- Context Window Recovery provides concrete numbered steps

## Summary
The document excels at structural organization but suffers from three main clarity issues: (1) critical operational thresholds are undefined (timeouts, data size limits, severity scales), (2) vague qualifiers like "meaningful," "relevant," and "appropriate" appear without operational definitions, and (3) implicit assumptions about reader knowledge create potential for misinterpretation. The most impactful improvements would be defining "meaningful unit of work," "expected timeframe," and "relevant context" with concrete, measurable criteria.
