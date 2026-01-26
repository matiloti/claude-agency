# Workflow-Skill Mapping

**Last Updated**: 2026-01-26
**Status**: Current

Maps workflows to their skill implementations (where applicable).

---

## Mapping Table

| Workflow | Skill | Status | Notes |
|----------|-------|--------|-------|
| Standard Feature Flow | N/A | Documentation only | Multi-session, requires orchestrator judgment at each step |
| Quick Fix Flow | `/quick-fix` | Implemented | Bounded, 4-step (including Step 0 validation) |
| Revision Flow | `/revision` | Implemented | Bounded, max 3 iterations, enforced limit |
| Parallel Work Conflict Protocol | N/A | Documentation only | Triggered mid-flow, context-dependent |
| Architecture Amendment Flow | N/A | Documentation only | Triggered mid-flow, requires architect judgment |
| Workflow Phase Rollback | N/A | Documentation only | Requires orchestrator judgment, user confirmation |
| Dynamic Workflow Classification | N/A | Documentation only | Entry-point decision logic, not a standalone workflow |

---

## Timestamp Format Standard

All skills use: `YYYY-MM-DDThh-mm-ss` for timestamped outputs.

Examples:
- Session logs: `2026-01-26T14-30-00_backend-developer_deck-crud-api.md`
- Feedback folders: `feedbacks/2026-01-26T14-30-00_claude-md-analysis/`

This format ensures:
- Lexicographic sorting matches chronological order
- No ambiguity between date and time components
- Filesystem compatibility (no colons)

---

## Why Some Workflows Are Not Skills

Workflows that meet ANY of the following criteria remain as documentation only:

1. **Multi-session**: Workflow spans multiple agent sessions with significant time gaps
2. **Orchestrator judgment required**: Each step requires contextual decisions that can't be pre-defined
3. **Triggered mid-flow**: Workflow is invoked as a sub-routine of another workflow
4. **User interaction required**: Multiple points where user input determines next steps
5. **Unbounded scope**: Workflow can expand based on discovered complexity

### Specific Rationale

| Workflow | Why Not a Skill |
|----------|-----------------|
| Standard Feature Flow | 14 steps across multiple sessions, each review requires judgment |
| Parallel Work Conflict Protocol | Triggered when blocker discovered, resolution varies by context |
| Architecture Amendment Flow | Triggered mid-implementation, architect makes judgment calls |
| Workflow Phase Rollback | Requires user confirmation, affects multiple artifacts |

### What Makes a Good Skill

Workflows that ARE skills share these characteristics:
- **Bounded**: Fixed number of steps with known endpoints
- **Self-contained**: Doesn't require orchestrator intervention mid-execution
- **Deterministic**: Same inputs produce predictable behavior
- **Atomic**: Either completes fully or fails cleanly

---

## Skill Locations

| Skill | Path |
|-------|------|
| `/quick-fix` | `.claude/skills/quick-fix/SKILL.md` |
| `/revision` | `.claude/skills/revision/SKILL.md` |
| `/feedback-pipeline` | `.claude/skills/feedback-pipeline/SKILL.md` |
| `/quick-feedback-capture` | `.claude/skills/quick-feedback-capture/SKILL.md` |

---

## Related Documentation

- Workflow definitions: `knowledge/orchestration/workflows/`
- Skill pattern reference: `.claude/skills/*/SKILL.md`
- Orchestration rules: `CLAUDE.md` (Orchestration Rules section)
