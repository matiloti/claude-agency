# Revision Flow

<!-- Last Updated: 2026-01-26 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

When the Judge issues `NEEDS_REVISION`, this flow handles the revision cycle with enforced iteration limits.

---

## Preconditions

- [ ] Judge has issued `NEEDS_REVISION` verdict
- [ ] Original agent output and files are available
- [ ] Judge feedback is documented in `knowledge/reviews/{agent-name}/`

---

## Steps

When the Judge issues `NEEDS_REVISION`:

0. **Validate iteration count** - Check if this is attempt 1, 2, or 3. If already at 3 failed attempts, skip to Escalation.
1. Orchestrator constructs a revision prompt (see `system/templates/prompts/README.md` for the Revision Prompt Template).
2. Spawn a new subagent with the full revision prompt.
3. The agent revises their work and creates fix commits.
4. Re-submit to the Judge for re-review.
5. Repeat until APPROVED (max 3 iterations, then escalate to user).

**Max iterations: 3** (hard limit, enforced by `/revision` skill)

---

## Exit States

- **Success**: Judge issues APPROVED verdict
- **Failure**: 3 iterations exhausted without APPROVED; escalated to user
- **Partial**: Dispute raised; awaiting Architect interpretation

---

## Interpretation Dispute Protocol

If a developer believes the Judge's feedback misinterprets the architecture or requirements:

1. **Developer flags dispute** in completion summary: "DISPUTE: [specific issue with Judge's interpretation]"
2. **Orchestrator pauses** the revision cycle
3. **Orchestrator spawns Architect** to provide authoritative interpretation of the disputed requirement
4. **Architect reviews**: original spec, Judge feedback, Developer's dispute
5. **Architect rules**: either confirms Judge's interpretation or clarifies the spec
6. **If spec clarified**: Orchestrator updates architecture docs and resumes with Developer
7. **If Judge confirmed**: Developer proceeds with revision using Judge's interpretation

---

## Escalation After 3 Revisions

When 3 revision attempts fail, escalate to the user with:

1. **Summary**: What was attempted and what keeps failing.
2. **Options**:
   - Accept as-is (with documented limitations).
   - Provide specific guidance for the agent to follow.
   - Change the approach entirely (trigger architecture amendment).
   - Defer the task to a later phase.
3. **Resumption**: After user provides direction, spawn a new subagent with the user's guidance included in the prompt.

---

## Related Documentation

- [Workflow Index](./README.md)
- `/revision` Skill: `.claude/skills/revision/SKILL.md`
- [Architecture Amendment Pipeline](../pipelines/architecture-amendment-pipeline.md)
- Workflow-skill mapping: `../workflow-skill-mapping.md`
