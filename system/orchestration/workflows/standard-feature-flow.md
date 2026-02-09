# Standard Feature Flow

<!-- Last Updated: 2026-01-27 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

The complete workflow for implementing a new feature. Use this for all Standard tier work.

---

## Preconditions

Before starting Standard Feature Flow:
- [ ] Incoming work has been classified (see [Dynamic Workflow Classification](./README.md#dynamic-workflow-classification))
- [ ] Work is confirmed as Standard tier (not Trivial or Epic)
- [ ] No active blockers in `knowledge/blockers/` affecting this feature
- [ ] User has confirmed/approved the feature concept

---

## Infrastructure Gate Checklist

Before Step 5 (Infrastructure Setup), answer these questions:

- [ ] **New service?** Does the feature require a new Docker container or service?
- [ ] **Schema change?** Does the feature require database migrations?
- [ ] **Environment variables?** Does the feature require new env vars in docker-compose or .env files?
- [ ] **Docker changes?** Does the feature require Dockerfile modifications or volume changes?

If ALL answers are No, skip Steps 5-6 and 11.

---

## Steps

The complete workflow for implementing a new feature:

1. **Ideate** - Ideator refines the idea and produces user stories.
2. **Review** - Judge reviews Ideator output.
3. **Architect** - Architect designs the technical solution.
4. **Review** - Judge reviews architecture.
5. **Infrastructure Setup** - Infrastructure Engineer ensures Docker/DB/env is ready for the feature (if needed).
6. **Review** - Judge reviews infrastructure changes (if any).
7. **Implement Backend** - Backend Developer implements with TDD.
8. **Review** - Judge reviews backend code.
9. **Implement Frontend** - Frontend Developer implements the UI.
10. **Review** - Judge reviews frontend code.
11. **Integration** - Infrastructure Engineer integrates new services, updates compose if needed.
12. **Test** - QA Tester validates the full feature.
13. **Regression Test** - QA Tester (or CI Agent) runs the full existing test suite (`./gradlew test` and `npm test`) to verify no regressions.
14. **iOS Testing** - Execute the [iOS Testing Pipeline](../pipelines/ios-testing-pipeline.md) with the new feature to verify visual/UX quality on iOS simulator.
15. **Review** - Judge reviews QA results and gives final verdict.

**Notes**:
- Steps 5-6 and 11 are skipped if the Infrastructure Gate Checklist results are all No.
- Steps 7-8 and 9-10 can run in parallel once architecture is approved (see [Parallel Work Protocol](./parallel-work-protocol.md)).

**QA Preparation**: After architecture approval (step 4), the QA Tester can begin writing test plans based on the approved architecture and user stories. This preparation work runs in parallel with implementation (steps 5-10) and does not require Judge review until the full feature test (step 12).

---

## Exit States

- **Success**: All 15 steps completed, final Judge verdict is APPROVED, code pushed to remote
- **Failure**: Judge issues REJECTED verdict at any step, or user cancels feature
- **Partial**: Feature blocked mid-workflow; partial work committed locally, blocker documented

---

## Workflow State Tracking

For each active feature, the orchestrator maintains state in `knowledge/workflow-state.md`:

```markdown
# Active Feature: {feature name}

**Current Phase**: {1-15}
**Started**: {ISO date}
**Last Updated**: {ISO date}

## Phase History
| Phase | Status | Agent | Date | Notes |
|-------|--------|-------|------|-------|
| 1. Ideate | completed | {project}-product-ideator | 2026-01-25 | US-DM-01 created |
| 2. Review | completed | judge | 2026-01-25 | APPROVED |
| 3. Architect | in_progress | architect | 2026-01-25 | |

## Blockers
- {any active blockers}

## Parallel Tracks (if applicable)
- Backend: {status}
- Frontend: {status}
```

The orchestrator updates this file before spawning each subagent.

---

## Related Documentation

- [Workflow Index](./README.md)
- [Parallel Work Protocol](./parallel-work-protocol.md)
- [Revision Flow](./revision-flow.md)
- [Phase Rollback](./phase-rollback.md)
- [iOS Testing Pipeline](../pipelines/ios-testing-pipeline.md)
