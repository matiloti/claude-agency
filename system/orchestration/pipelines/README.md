# Orchestration Pipelines

<!-- Last Updated: 2026-01-27 -->
<!-- Status: Current -->

This folder contains linear, staged analysis processes (pipelines) for the multi-agent system. Unlike workflows which may involve complex branching and multi-actor coordination, pipelines are sequential staged processes with clear phase progression.

---

## Table of Contents

| Pipeline | File | When to Use |
|----------|------|-------------|
| Agent Analysis Pipeline | [agent-analysis-pipeline.md](./agent-analysis-pipeline.md) | Periodic system health checks, after adding multiple agents, before major refactoring |
| Architecture Amendment Pipeline | [architecture-amendment-pipeline.md](./architecture-amendment-pipeline.md) | Implementation reveals design flaw requiring architectural change |
| iOS Testing Pipeline | [ios-testing-pipeline.md](./ios-testing-pipeline.md) | Comprehensive iOS app testing with visual/UX inspection, pre-release QA |
| Prompt Analysis Pipeline | [prompt-analysis-pipeline.md](./prompt-analysis-pipeline.md) | Periodic prompt health checks, after adding prompts/templates, when agents exhibit inconsistent behavior |
| Workflow Analysis Pipeline | [workflow-analysis-pipeline.md](./workflow-analysis-pipeline.md) | Verify terminology, identify gaps/redundancies, assess workflow-skill mapping |

---

## Pipeline vs Workflow Distinction

**Pipelines** are used for:
- Linear, staged processes with clear phase progression
- Analysis and review processes
- Sequential transformations where output of one phase feeds the next

**Workflows** are used for:
- Complex multi-actor coordination with branching
- Event-driven processes with multiple possible paths
- Iterative processes with revision loops

---

## Related Documentation

- Workflows: `../workflows/README.md`
- Continuous Improvement: `../continuous-improvement.md`
- Formats: `../../formats/` (session-log, review-report, handoff, feedback-item)
