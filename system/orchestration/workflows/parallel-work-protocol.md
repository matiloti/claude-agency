# Parallel Work Conflict Protocol

<!-- Last Updated: 2026-01-26 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

When Frontend and Backend work in parallel (after architecture approval), this protocol handles coordination and conflicts.

---

## 1. Discovery of Blocking Issue

If a parallel agent discovers the architecture is incomplete, incorrect, or ambiguous mid-implementation:
- The agent STOPS work on the affected area.
- Documents the issue in its completion summary under "Blockers Encountered."
- Reports completion of what it CAN finish without the blocked work.

---

## 2. Orchestrator Response

- Holds pending work for the other parallel track (does not spawn new subagent tasks for it until blocker is resolved).
- Creates a `knowledge/blockers/{agent}-{issue-slug}.md` file describing the issue.
- Spawns the Architect for an amendment (see [Architecture Amendment Flow](./architecture-amendment-flow.md)).
- After amendment is approved by Judge, notifies both agents of the resolution.
- Resumes parallel work with updated architecture context.

---

## 3. Communication

Parallel agents communicate through `knowledge/handoffs/` files. For parallel work, handoffs are created upon completion of each parallel track. The second-to-complete agent reads the first's handoff before finalizing.

---

## 4. Simultaneous Blockers

If both parallel agents report blockers simultaneously, the orchestrator prioritizes by:
1. Architectural blockers over implementation blockers
2. Backend over frontend if both architectural

Resolve the prioritized blocker first.

---

## Related Documentation

- [Workflow Index](./README.md)
- [Standard Feature Flow](./standard-feature-flow.md) - Steps 7-10 reference parallel work
- [Architecture Amendment Flow](./architecture-amendment-flow.md)
- Handoff Protocol: `CLAUDE.md` (Handoff Protocol section)
