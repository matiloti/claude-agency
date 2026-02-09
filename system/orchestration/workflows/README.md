# Orchestration Workflows

<!-- Last Updated: 2026-01-26 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

This folder contains all orchestration workflows for the flashcards app multi-agent system. The orchestrator follows these workflows when coordinating subagents.

---

## Table of Contents

| Workflow | File | When to Use |
|----------|------|-------------|
| Standard Feature Flow | [standard-feature-flow.md](./standard-feature-flow.md) | New features, endpoints, screens |
| Quick Fix Flow | [quick-fix-flow.md](./quick-fix-flow.md) | Config fixes, typos, dependency bumps |
| Revision Flow | [revision-flow.md](./revision-flow.md) | After NEEDS_REVISION verdict |
| Parallel Work Protocol | [parallel-work-protocol.md](./parallel-work-protocol.md) | Backend + Frontend after architecture |
| Phase Rollback | [phase-rollback.md](./phase-rollback.md) | Fundamental flaw in earlier phase |

---

## Dynamic Workflow Classification

**Entry Point**: Use this section FIRST to classify incoming work before proceeding.

Not all work requires the full Standard Feature Flow. Classify incoming work into one of three tiers:

```
                    +---------------------+
                    |   Incoming Work     |
                    +----------+----------+
                               |
                    +----------v----------+
                    | Run Trivial Check   |
                    | (6-item checklist)  |
                    +----------+----------+
                               |
              +----------------+----------------+
              | ALL true       |                | ANY false
              v                |                v
    +-----------------+       |      +-----------------+
    |   TRIVIAL       |       |      | Multi-feature?  |
    |  Quick Fix Flow |       |      | Major refactor? |
    +-----------------+       |      +--------+--------+
                              |               |
                              |    +----------+----------+
                              |    | No       |          | Yes
                              |    v          |          v
                              |  +-------------+  +-------------+
                              |  |  STANDARD   |  |    EPIC     |
                              |  | Feature Flow|  | Sub-feature |
                              |  +-------------+  |   per Flow  |
                              |                   +-------------+
```

| Tier | Criteria | Workflow | Skill |
|------|----------|----------|-------|
| **Trivial** | Config fix, typo, dependency bump, env var change | Quick Fix Flow (agent -> Judge -> push) | `/quick-fix` |
| **Standard** | New feature, new endpoint, new screen, schema change | Standard Feature Flow (full workflow) | N/A |
| **Epic** | Multi-feature initiative, major refactor, new system | Standard Flow per sub-feature, with umbrella tracking | N/A |

### Epic Work

For **Epic** work:
1. Ideator breaks the epic into discrete features.
2. Architect designs the overall approach and per-feature plans.
3. Each sub-feature follows Standard Flow independently.
4. QA performs integration testing across all sub-features at the end.

---

## Judge Verdict Reference

All Judge reviews produce one of the following verdicts. Orchestrator actions depend on the verdict.

| Verdict | Meaning | Required Orchestrator Action |
|---------|---------|------------------------------|
| `APPROVED` | Work meets all quality standards and requirements | Proceed to next phase; push if final review |
| `NEEDS_REVISION` | Work has fixable issues; agent should revise | Trigger Revision Flow (max 3 iterations) |
| `REJECTED` | Fundamental problems; current approach won't work | Escalate to user immediately; do NOT attempt revision |
| `ROLLBACK_TO_PHASE_{N}` | Earlier phase has fundamental flaw | Trigger Workflow Phase Rollback to phase N |

### Verdict Decision Tree for Skills

```
Judge Verdict
    |
    +---> APPROVED -----------> Continue workflow / Push if final
    |
    +---> NEEDS_REVISION -----> Invoke /revision skill (max 3 iterations)
    |                            |
    |                            +---> If 3 failures -> Escalate to user
    |
    +---> REJECTED -----------> Escalate to user (do NOT retry)
    |
    +---> ROLLBACK_TO_PHASE_{N} -> Archive artifacts (_superseded)
                                 -> Restart from phase N
```

---

## State Machine Definition

Valid phase statuses and transitions:

| Status | Meaning | Valid Transitions |
|--------|---------|-------------------|
| `pending` | Phase not yet started | -> `in_progress` |
| `in_progress` | Agent actively working | -> `completed`, `blocked`, `failed` |
| `completed` | Phase finished successfully | (terminal for this phase) |
| `blocked` | Waiting on external dependency | -> `in_progress` (after unblock) |
| `failed` | Phase failed, requires intervention | -> `in_progress` (after resolution) |

**Terminal states**: `completed` (success), `failed` + escalation (failure)

**Error handling**:
- If agent fails mid-phase: mark `failed`, document in blockers, escalate
- If blocked: mark `blocked`, document dependency, resolve before resuming
- Never skip a blocked phase; either resolve or escalate

---

## Related Orchestration Rules

The following orchestration rules (from CLAUDE.md) reference these workflows:

- **Rule 3**: Always trigger a Judge review after any agent completes a task (all workflows)
- **Rule 7**: Frontend and Backend can work in parallel once architecture is approved (Parallel Work Protocol)
- **Rule 8**: Sequential dependencies: Ideation -> Architecture -> Infrastructure -> Implementation -> Integration -> Testing (Standard Feature Flow)
- **Rule 9**: Quality gates: No phase proceeds until the Judge approves the previous phase's output (all workflows)

---

## Related Skills

Some bounded workflows have corresponding skills:
- **Quick Fix Flow** -> `.claude/skills/quick-fix/SKILL.md`
- **Revision Flow** -> `.claude/skills/revision/SKILL.md`

See `../workflow-skill-mapping.md` for the complete mapping and rationale.
