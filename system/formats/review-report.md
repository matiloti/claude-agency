# Review Report Format

Standard format for Judge review reports.

---

## Review Report Template

```markdown
# [Task/Feature Name] Review

**Review Date**: [YYYY-MM-DD]
**Reviewer**: [judge agent name]
**Reviewed Agent**: [agent being reviewed]
**Phase**: [workflow phase or task context]

---

## Executive Summary

**Verdict**: **[APPROVED / NEEDS_REVISION / REJECTED]**

[2-3 sentence summary of the review outcome and key findings]

---

## Review Checklist

| Requirement | Status | Notes |
|-------------|--------|-------|
| [Requirement 1] | PASS/FAIL | [Details] |
| [Requirement 2] | PASS/FAIL | [Details] |
| ... | ... | ... |

---

## Detailed Analysis

### 1. [Category 1 - e.g., TDD Compliance]

**Rating**: [EXCELLENT / GOOD / SATISFACTORY / NEEDS_WORK / POOR]

[Detailed analysis with code examples if applicable]

### 2. [Category 2 - e.g., API Compliance]

**Rating**: [EXCELLENT / GOOD / SATISFACTORY / NEEDS_WORK / POOR]

[Detailed analysis]

[Continue with relevant categories...]

---

## Issues Found

### Critical Issues
[List or "None."]

### Major Issues
[List with location, description, impact, recommendation - or "None."]

### Minor Issues
[List with location, description, impact, recommendation - or "None."]

---

## Final Verdict

### [APPROVED / NEEDS_REVISION / REJECTED]

[Summary of why this verdict was reached]

[For NEEDS_REVISION: specific items that must be addressed]
[For REJECTED: fundamental problems that require re-design]

---

## Recommendations

[Optional section for future improvements not blocking approval]

---

**Signed**: [judge agent name]
**Date**: [YYYY-MM-DD]
```

---

## Verdict Definitions

| Verdict | Meaning | Next Action |
|---------|---------|-------------|
| `APPROVED` | Work meets all requirements | Proceed to next phase |
| `NEEDS_REVISION` | Minor issues require fixing | Agent fixes and re-submits (max 3 attempts) |
| `REJECTED` | Fundamental problems | Escalate to orchestrator for re-design decision |

---

## File Location

Review reports are stored in: `knowledge/reviews/{agent-name}/`

Filename format: `{task-name}-review.md`

Example: `knowledge/reviews/backend-developer/deck-crud-api-review.md`
