# Architecture Amendment Flow

<!-- Last Updated: 2026-01-26 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

When implementation reveals architectural flaws, this flow handles amendments to the architecture.

---

## Flaw Classification Rubric

Before triggering an amendment, classify the issue:

| Classification | Criteria | Action |
|----------------|----------|--------|
| **Amendment Required** | API contract change (new/modified endpoints, response shapes), schema change (new tables, columns), security boundary change | Proceed with Architecture Amendment Flow |
| **Clarification Only** | Missing detail in spec that doesn't change contracts, ambiguous wording, edge case not covered | Developer asks Architect for clarification; no formal amendment |
| **Developer Misunderstanding** | Spec covers the case but developer missed it, terminology confusion | Developer re-reads existing docs; no amendment needed |

---

## Steps

1. Developer documents the issue in their completion summary.
2. Orchestrator spawns Architect with:
   - Original architecture files
   - Developer's issue description
   - Current implementation state
3. Architect produces an amendment (updated API spec, schema change, etc.).
4. Judge reviews the amendment.
5. If approved, orchestrator notifies affected agents and provides updated architecture context.

---

## Related Documentation

- [Workflow Index](./README.md)
- [Parallel Work Protocol](./parallel-work-protocol.md)
- [Phase Rollback](./phase-rollback.md)
