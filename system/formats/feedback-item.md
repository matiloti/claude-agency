# Feedback Item Format

Standard format for feedback items in the continuous improvement system.

---

## Feedback Item Template

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
- **State**: pending
- **Created**: [ISO 8601 timestamp]
- **Last updated**: [ISO 8601 timestamp]
```

---

## Feedback Lifecycle States

| State | Location | Meaning |
|-------|----------|---------|
| `pending` | `feedbacks/feedback-ideas/` | Feedback added, not yet reviewed |
| `analyzing` | `feedbacks/feedback-ideas/` | Retrospective Agent evaluating |
| `approved` | `feedbacks/approved/` | User approved for implementation |
| `rejected` | (deleted or archived) | User rejected the item |
| `implementing` | `feedbacks/approved/` | Agent(s) making changes |
| `reviewing` | `feedbacks/approved/` | Judge evaluating changes |
| `completed` | `feedbacks/implemented/` | Changes verified working |
| `reverted` | `feedbacks/implemented/` | Changes rolled back |

---

## Quick Feedback Capture

For rapid feedback capture (under 60 seconds), use minimal format:

```markdown
# [Brief title]

[1-3 sentences describing the feedback idea]

---
*Captured: [ISO 8601 timestamp]*
*Source: [user/session/observation]*
```

Full analysis is added later during the feedback processing workflow.

---

## File Location

Feedback items are stored in project-level `feedbacks/` folder:

```
feedbacks/
  feedback-ideas/     # New items awaiting review
  approved/           # User-approved, ready for implementation
  implemented/        # Completed items
```

Filename format: `{topic-slug}.md` or `{YYYYMMDD}_{topic-slug}.md`

Example: `feedbacks/feedback-ideas/improve-error-messages.md`
