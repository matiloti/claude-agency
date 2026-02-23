# Standard Feature Flow

<!-- Last Updated: 2026-02-23 -->
<!-- Status: Current -->
<!-- Author: workflow-expert -->

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

## E2E Gate Policy

Before Step 14 (E2E Testing), confirm the project's pipeline status:

- [ ] **Supported pipeline?** Does a supported E2E pipeline exist for this project type? (see [E2E Pipeline Selection](#e2e-pipeline-selection))

**E2E Gate is REQUIRED for all projects with a supported pipeline.** Steps 14, 14a, and 14b MUST be executed. Skip the E2E Gate only if no pipeline exists for the project type, and document this explicitly in the workflow state file under `E2E_Status: no_pipeline`.

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
14. **E2E Testing** - Execute the full E2E pipeline (Phases 0-5) for the project type. Captures screenshots at every state change and generates a structured report. (See [E2E Pipeline Selection](#e2e-pipeline-selection) and [E2E Gate Detail](#e2e-gate-detail) below.)
    - **14a. Visual QA Review** - QA agent reviews ALL screenshots from the E2E run against the Visual Inspection Checklist. This is a deliberate, non-automated review step.
      - **PASS**: All screens pass visual QA → proceed to Step 15.
      - **FAIL**: Any screen fails → Step 14 transitions to `needs_revision`. Create a visual QA feedback file with specific, screen-identified issues → loop back to Step 14b.
    - **14b. Frontend Fix** *(on FAIL only)* - Frontend Developer receives the visual QA feedback file, fixes identified issues, and commits locally. After fix, the loop restarts at Step 14.
15. **Review** - Judge reviews all QA results (unit, regression, E2E report, and visual QA outcome) and gives final verdict.

**Notes**:
- Steps 5-6 and 11 are skipped if the Infrastructure Gate Checklist results are all No.
- Steps 7-8 and 9-10 can run in parallel once architecture is approved (see [Parallel Work Protocol](./parallel-work-protocol.md)).
- Step 14 (E2E Gate) is REQUIRED for projects with a supported pipeline. Skip only if no pipeline exists, documented in workflow state.
- The E2E feedback loop (14 → 14a → FAIL → 14b → 14) has a maximum of **3 iterations**. After 3 consecutive visual QA failures, escalate to the user before proceeding.

---

## E2E Gate Detail

Step 14 is a quality gate, not a single task. It contains an internal feedback loop that repeats until visual QA passes or the iteration limit is reached.

### Flow Diagram

```
Step 13 (Regression Test) complete
        │
        ▼
┌───────────────────┐
│  Step 14          │◄──────────────────────────────┐
│  E2E Pipeline Run │                               │
│  (Phases 0–5)     │                               │
└────────┬──────────┘                               │
         │ screenshots + report generated           │
         ▼                                          │
┌───────────────────┐                               │
│  Step 14a         │                               │
│  Visual QA Review │                               │
│  (agent reviews   │                               │
│   all screenshots)│                               │
└────────┬──────────┘                               │
         │                                          │
    ┌────┴────┐                                     │
    │  PASS?  │                                     │
    └────┬────┘                                     │
    YES  │  NO                                      │
         │   └──► Create visual QA feedback file    │
         │        Record iteration N in state       │
         │                                          │
         │        ┌───────────────────┐             │
         │        │  Step 14b         │             │
         │        │  Frontend Fix     │             │
         │        │  (fix issues,     │             │
         │        │   commit locally) │             │
         │        └────────┬──────────┘             │
         │                 │                        │
         │          iteration < 3? ────────────────►┘
         │                 │
         │          iteration = 3?
         │                 │
         │                 └──► ESCALATE TO USER
         │
         ▼
    Step 15 (Review)
```

### State Transitions for Step 14

Step 14 uses an extended phase-level state model in addition to the global state machine:

| E2E State | Meaning | Valid Next States |
|-----------|---------|-------------------|
| `in_progress` | E2E pipeline running | `visual_qa_review` |
| `visual_qa_review` | Agent reviewing screenshots | `pass`, `needs_revision` |
| `pass` | All screens pass visual QA | (terminal; proceed to Step 15) |
| `needs_revision` | Visual QA failed; feedback issued | `in_progress` (next iteration) |
| `escalated` | 3 iterations failed; waiting on user | `in_progress` (if user approves retry) |

### Visual QA Review Checklist (Step 14a)

The QA agent applies the following checklist to every screenshot captured during the E2E run:

- [ ] No broken layouts or elements outside their containers
- [ ] No horizontal or vertical content overflow
- [ ] Consistent spacing and padding across equivalent components
- [ ] Proper alignment (text, icons, buttons, cards)
- [ ] No cut-off or truncated text where full text is expected
- [ ] Dark theme applied consistently (if applicable)
- [ ] Font sizes and weights are consistent with the design system
- [ ] Navigation elements are clearly visible and accessible
- [ ] Responsive behavior is correct at all tested viewports (mobile, tablet, desktop)
- [ ] Interactive elements (buttons, links, inputs) have correct visual state

### Visual QA Feedback File

When visual QA fails (Step 14a FAIL), the QA agent creates a feedback file at:

```
knowledge/e2e-feedback/visual-qa-iteration-{N}.md
```

The file must contain specific, actionable issues in this format:

```markdown
# Visual QA Feedback — Iteration {N}

**Date**: {ISO date}
**E2E Run**: {pipeline run identifier or screenshot directory}
**Iteration**: {N} of 3

## Issues Found

| # | Screen / Route | Viewport | Issue | Screenshot |
|---|---------------|----------|-------|------------|
| 1 | /dashboard    | 768px    | Table overflows container horizontally | screenshot-dashboard-768.png |
| 2 | /dashboard    | 375px    | Cards overflow parent container on mobile | screenshot-dashboard-mobile.png |
| 3 | /settings     | 1280px   | Truncated label text in form row 3 | screenshot-settings-desktop.png |

## Required Fixes

- [ ] Issue 1: Fix table horizontal overflow on tablet viewport in dashboard
- [ ] Issue 2: Constrain card widths within mobile container on dashboard
- [ ] Issue 3: Allow label text to wrap or increase row height in settings form
```

### E2E Iteration Limit

- **Iterations 1-2**: Standard loop — fix and re-run.
- **Iteration 3 failure**: Do NOT loop again. Set `E2E_Status: escalated` in workflow state. Notify the user with a summary of all three feedback files. The user decides: approve visual issues as acceptable, request a 4th iteration, or cancel the feature.

---

## Agent Selection by Tech Stack

The orchestrator selects agents based on the project's tech stack declared in its `CLAUDE.md`.

### Frontend Agents

| Tech Stack | Developer Agent | Code Judge |
|-----------|----------------|------------|
| React Native + Expo | `react-native-expo-frontend-developer` | `react-native-expo-code-judge` |
| React + Vite + Tailwind (web) | `react-vite-frontend-developer` | `react-vite-code-judge` |

### Backend Agents

| Tech Stack | Developer Agent | Code Judge |
|-----------|----------------|------------|
| Kotlin + Spring Boot + PostgreSQL | `kotlin-spring-boot-backend-developer` | `kotlin-spring-boot-code-judge` |

### Cross-Stack Judges

| Stack Combination | Judge |
|-------------------|-------|
| Kotlin Spring Boot + React Native | `kotlin-spring-react-native-judge` |
| Kotlin Spring Boot + React Web | `kotlin-spring-react-vite-judge` |

### Other Agents (stack-agnostic)

These agents work with any tech stack:
- `base-product-ideator` / `{project}-product-ideator`
- `{stack}-architect`
- `infrastructure-engineer` / `infrastructure-judge`
- `qa-tester` / `qa-judge`
- `documentation`
- `retrospective-analyst`

---

## E2E Pipeline Selection

| Project Type | Pipeline | Notes |
|-------------|----------|-------|
| React Native / Expo (iOS) | [iOS Testing Pipeline](../pipelines/ios-testing-pipeline.md) | Requires Mac with Xcode + Simulator |
| React web app | [React Playwright E2E Pipeline](../pipelines/react-playwright-e2e-pipeline.md) | Requires Playwright + Chromium |

**QA Preparation**: After architecture approval (step 4), the QA Tester can begin writing test plans based on the approved architecture and user stories. This preparation work runs in parallel with implementation (steps 5-10) and does not require Judge review until the full feature test (step 12).

---

## Exit States

- **Success**: All steps completed, E2E visual QA result is PASS, final Judge verdict is APPROVED, code pushed to remote.
- **Failure**: Judge issues REJECTED verdict at any step, or user cancels feature.
- **E2E Escalation**: E2E visual QA fails after 3 iterations; workflow pauses and escalates to user for decision.
- **Partial**: Feature blocked mid-workflow; partial work committed locally, blocker documented.

---

## Workflow State Tracking

For each active feature, the orchestrator maintains state in `knowledge/workflow-state.md`:

```markdown
# Active Feature: {feature name}

**Current Phase**: {1-15}
**Started**: {ISO date}
**Last Updated**: {ISO date}

## E2E Gate State
**E2E_Iteration**: {1 | 2 | 3 | —}
**E2E_Status**: {running | visual_qa_review | pass | needs_revision | fail_iteration_1 | fail_iteration_2 | fail_iteration_3 | escalated | no_pipeline}

## Phase History
| Phase | Status | Agent | Date | Notes |
|-------|--------|-------|------|-------|
| 1. Ideate | completed | {project}-product-ideator | 2026-01-25 | US-DM-01 created |
| 2. Review | completed | judge | 2026-01-25 | APPROVED |
| 3. Architect | in_progress | architect | 2026-01-25 | |
| 14. E2E Testing (iter 1) | needs_revision | qa-tester | {date} | visual-qa-iteration-1.md created |
| 14a. Visual QA Review (iter 1) | completed | qa-tester | {date} | 3 issues found |
| 14b. Frontend Fix (iter 1) | completed | react-vite-frontend-developer | {date} | overflow + truncation fixed |
| 14. E2E Testing (iter 2) | completed | qa-tester | {date} | |
| 14a. Visual QA Review (iter 2) | pass | qa-tester | {date} | all screens passed |

## Blockers
- {any active blockers}

## Parallel Tracks (if applicable)
- Backend: {status}
- Frontend: {status}
```

The orchestrator updates this file before spawning each subagent. When the E2E loop is active, each iteration appends new rows to the Phase History table rather than overwriting existing rows, preserving the full iteration audit trail.

---

## Related Documentation

- [Workflow Index](./README.md)
- [Parallel Work Protocol](./parallel-work-protocol.md)
- [Revision Flow](./revision-flow.md)
- [Phase Rollback](./phase-rollback.md)
- [iOS Testing Pipeline](../pipelines/ios-testing-pipeline.md)
- [React Playwright E2E Pipeline](../pipelines/react-playwright-e2e-pipeline.md)
