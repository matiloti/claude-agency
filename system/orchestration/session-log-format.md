# Session Log Formats

<!-- Last Updated: 2026-01-25 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

This document contains the session log format templates for the flashcards app multi-agent system.

## Subagent Session Log Format

Each subagent session log file MUST contain:

```markdown
# Session Log

- **Timestamp**: [ISO 8601 timestamp]
- **Agent**: [agent role name]
- **Spawned By**: [who initiated this task]
- **Task**: [brief task description]
- **Status**: [completed / blocked / needs-revision]

## Work Summary
[2-5 sentence summary of what was accomplished]

## Artifacts Created/Modified
- [list of files created or modified]

## Key Decisions
- [any decisions made during the session]

## Outcome
[Final result - what was delivered, or why it was blocked]
```

## Orchestrator Session Log Format

The orchestrator must also create session logs for coordination work:

```markdown
# Session Log

- **Timestamp**: [ISO 8601 timestamp]
- **Agent**: orchestrator
- **Spawned By**: [user / scheduled:{trigger} / hook:{name}]
- **Task**: [brief coordination task description]
- **Status**: [completed / in-progress / blocked]

## Coordination Summary
[What was coordinated, which agents were spawned, what decisions were made]

## Agents Spawned
- [agent-name]: [task description] -> [outcome]

## Decisions Made
- [any routing/workflow decisions]

## Outcome
[What was accomplished in this orchestration session]
```

## Spawned By Vocabulary

The `Spawned By` field uses a controlled vocabulary:

| Value | Meaning |
|-------|---------|
| `user` | Direct user request |
| `orchestrator` | Orchestrator following workflow |
| `{agent-name}` | Another agent (escalation or handoff) |
| `scheduled:{trigger}` | Automated trigger (e.g., `scheduled:retrospective-3-features`) |
| `hook:{name}` | System hook (e.g., `hook:ci-failure`) |

### Examples

- `Spawned By: user` - User asked for this directly
- `Spawned By: orchestrator` - Orchestrator delegated per workflow
- `Spawned By: judge` - Judge requested revision
- `Spawned By: scheduled:retrospective-3-features` - Automatic retrospective trigger
