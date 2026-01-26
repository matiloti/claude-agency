# Quick Feedback Capture

> Owner: Orchestrator | Version: 1.0 | Last Updated: 2026-01-26

Rapidly capture a feedback idea without interrupting the current workflow.

**CRITICAL: Complete capture in under 60 seconds. This IS the value proposition.**

## Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| idea | Yes | The feedback idea to capture (from conversation context) |

## Preconditions

- [ ] Idea can be captured in under 60 seconds
- [ ] Not a formal feedback item requiring analysis (use `/feedback-pipeline` for those)
- [ ] Not a bug/blocker (use `knowledge/blockers/` or `ISSUES.md`)

## Exit States

- **Success**: Quick idea file created in `feedbacks/feedback-ideas/`
- **Failure**: Idea too complex for quick capture (redirect to `/feedback-pipeline`)
- **Partial**: N/A (quick capture is atomic)

---

## When to Use

Use this skill when:
- The user says "note down this feedback idea" or similar
- An insight arises during active work that should be recorded
- You need to capture something quickly without losing focus on the current task

Do NOT use this skill for:
- Formal feedback requiring analysis (use `/feedback-pipeline` instead)
- Bugs or blockers (use `knowledge/blockers/` or `ISSUES.md`)
- Questions requiring discussion (use `knowledge/questions/`)

---

## Workflow

### Step 1: Create the File

**Filename format**: `{YYYY-MM-DD}T{HH-MM}_{short-slug}.md`

- Use current timestamp with hours and minutes
- Keep slug short but descriptive (3-5 words, kebab-case)
- Example: `2026-01-26T14-30_orchestrator-judge-bias.md`

**Location**: `feedbacks/feedback-ideas/`

### Step 2: Fill the Template

Use the minimal template below. Fill ONLY required fields during capture.

```markdown
# Idea: {Short Title}

**Date**: {YYYY-MM-DD}
**Source**: {user / orchestrator observation / agent observation}
**Category**: {suggested: workflow, agents, templates, knowledge, tooling, other; feel free to use your own}

## Summary

{1-2 sentences: what is the idea?}

## Description

{Brief explanation - keep it short during capture}

## Pending Questions

- {Optional: questions to resolve later}

## Status

- [x] Captured
- [ ] Reviewed
- [ ] Promoted to formal feedback
- [ ] Dismissed

---

**Related Work**: {optional: feature/task being worked on when idea arose}
**Session Log Reference**: {optional: path to session log for context}
**Urgency**: {optional: low / medium / high}

### Dismissal (if applicable)

- [ ] Dismissed
- **Reason**: {fill if dismissed}
```

### Step 3: Return Immediately

After creating the file, return to the current task. Do NOT:
- Elaborate on the idea
- Run the feedback pipeline
- Update indexes or cross-references
- Create a session log for this operation

---

## Template Fields Reference

| Field | Required | Description |
|-------|----------|-------------|
| Title | Yes | Short, descriptive title |
| Date | Yes | Capture date (YYYY-MM-DD) |
| Source | Yes | Who/what raised the idea |
| Category | Yes | Free-form category (suggestions provided) |
| Summary | Yes | 1-2 sentence overview |
| Description | Yes | Brief explanation |
| Pending Questions | No | Questions to resolve during elaboration |
| Related Work | No | Context about current task |
| Session Log Reference | No | Link to session for additional context |
| Urgency | No | Priority indicator |
| Dismissed checkbox | No | Mark if idea is discarded |
| Dismissal Reason | No | Why the idea was discarded |

---

## Lifecycle

```
CAPTURED --> REVIEWED --> PROMOTED
                |
                +--> DISCARDED
```

| State | Meaning |
|-------|---------|
| Captured | Initial quick note (this file exists) |
| Reviewed | User has looked at it, may add notes |
| Promoted | User requested formal feedback processing |
| Discarded | Not worth pursuing |

**Promotion is USER-INITIATED ONLY.** There are no automatic triggers or time-based rules.

---

## Promotion Workflow

When the user requests promotion of a quick idea:

1. **Check for conflicts**: Scan `feedbacks/*/final_feedback.md` for related items
2. **Run feedback-pipeline**: `/feedback-pipeline feedbacks/feedback-ideas/{filename}`
3. **Update status**: Mark the quick idea as "Promoted to formal feedback"
4. **Link**: Add reference to the formal feedback folder in the quick idea file

---

## Dismissal Workflow

When the user decides an idea is not worth pursuing:

1. **Mark dismissed**: Check the `[ ] Dismissed` checkbox in the file
2. **Add reason**: Fill the "Reason" field explaining why
3. **Rename file**: Add `_DISCARDED` suffix (e.g., `2026-01-26T14-30_idea_DISCARDED.md`)
4. **Move file**: Move to `feedbacks/feedback-ideas/discarded/`
5. **Update INDEX.md**: Change status to "Discarded"

---

## Session Logging Exemption

Quick feedback capture is EXEMPT from Rule 19 (Orchestrator session logging).

The quick note file itself serves as the audit trail. Creating a session log for a 60-second operation would defeat the purpose.

---

## Examples

### Good Quick Capture (35 seconds)

```markdown
# Idea: Judge Should Not See Agent Self-Scores

**Date**: 2026-01-26
**Source**: User observation
**Category**: workflow

## Summary

Orchestrator biases Judge by including agent's self-reported scores in review prompts.

## Description

When spawning Judge, orchestrator includes metrics like "100% pass rate" from the agent. This creates confirmation bias.

## Pending Questions

- Should all numeric metrics be excluded or just self-assessments?

## Status

- [x] Captured
- [ ] Reviewed
- [ ] Promoted to formal feedback
- [ ] Dismissed
```

### Bad Capture (too elaborate, would take >60 seconds)

```markdown
# Idea: Comprehensive Analysis of Judge Review Bias Patterns

**Date**: 2026-01-26
**Source**: User observation
**Category**: workflow

## Summary

There appears to be a systematic pattern where the orchestrator inadvertently biases
the Judge agent by providing pre-summarized results, implied outcomes, and numeric
metrics that create confirmation bias...

[continues for 3 more paragraphs]

## Detailed Analysis

### Root Cause Investigation
...

### Historical Examples
...

### Comparative Analysis with Other Systems
...
```

The second example should be a formal feedback item, not a quick capture.

---

## Constraints

- **60 seconds maximum** - This is immutable
- **No session logging** - Exempt from Rule 19
- **No elaboration** - Save it for promotion
- **User controls promotion** - Never auto-promote
- **Free-form categories** - Never standardize into fixed list
