# System Formats

This folder contains standard format templates used across all projects in the agency.

## Available Formats

| Format | File | Purpose |
|--------|------|---------|
| Session Log | `session-log.md` | Subagent and orchestrator session logging |
| Review Report | `review-report.md` | Judge review verdicts and analysis |
| Handoff | `handoff.md` | Agent-to-agent work transitions |
| Feedback Item | `feedback-item.md` | Continuous improvement feedback entries |
| Design Specification | `design-specification.md` | UX/UI design specs for features |

## Usage

Agents reference these formats when creating artifacts. Templates reference formats using:

```
Follow format per `system/formats/{format-name}.md`
```

## Adding New Formats

When adding a new standardized format:

1. Create `{format-name}.md` in this folder
2. Include a template section with markdown code block
3. Document any variants (e.g., minimal vs. extended)
4. Specify file location and naming conventions
5. Update this README
