# Feedbacks System

This folder contains feedback items for system improvements. Feedback is processed through the Continuous Improvement Workflow defined in `system/orchestration/continuous-improvement.md`.

## Folder Structure

```
feedbacks/
  feedback-ideas/     # New feedback items awaiting review
  approved/           # User-approved items ready for implementation
  implemented/        # Completed feedback items
```

## Feedback File Format

Each feedback item is a markdown file with the following structure:

```markdown
# [Short descriptive title]

## Summary
[1-2 sentence description of the feedback]

## Details
[Detailed explanation of the issue, improvement, or suggestion]

## Impact
- **Affected files**: [list of files that would change]
- **Effort estimate**: low | medium | high
- **Risk level**: low | medium | high

## Status
- **State**: pending | analyzing | approved | rejected | implementing | reviewing | completed | reverted
- **Created**: [ISO 8601 timestamp]
- **Last updated**: [ISO 8601 timestamp]
```

## Feedback Lifecycle States

| State | Location | Meaning |
|-------|----------|---------|
| `pending` | `feedback-ideas/` | Feedback added, not yet reviewed |
| `analyzing` | `feedback-ideas/` | Retrospective Agent evaluating |
| `approved` | `approved/` | User approved for implementation |
| `rejected` | (deleted or archived) | User rejected the item |
| `implementing` | `approved/` | Agent(s) making changes |
| `reviewing` | `approved/` | Judge evaluating changes |
| `completed` | `implemented/` | Changes verified working |
| `reverted` | `implemented/` | Changes rolled back |

## Processing Rules

1. New feedback items go into `feedback-ideas/`
2. When approved by user, move to `approved/`
3. When implementation is complete and verified, move to `implemented/`
4. Never implement feedback without:
   - User approval
   - A snapshot taken by Infrastructure Engineer
   - Judge review after implementation

## Related Documentation

- `system/orchestration/continuous-improvement.md` - Full workflow details
- `system/orchestration/workflows/phase-rollback.md` - Rollback procedures
- `system/formats/feedback-item.md` - Detailed feedback item format template
