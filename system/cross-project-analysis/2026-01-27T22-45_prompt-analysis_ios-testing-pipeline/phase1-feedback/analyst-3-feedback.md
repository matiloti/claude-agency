# Prompt Analysis Feedback - Analyst 3

**Focus Area**: Completeness (missing context, prerequisites, edge cases, examples)
**Documents Analyzed**: `ios-testing-pipeline.md`
**Date**: 2026-01-27T22:45:00Z

## ios-testing-pipeline.md

### Strengths
- Comprehensive phase breakdown with clear objectives
- Troubleshooting section covers common issues
- Screenshot naming convention with examples
- Report template provides complete structure
- Gate checkpoints ensure phase completion
- MCP tool quick reference aids execution

### Issues

| ID | Severity | Category | Description | Location | Suggested Fix |
|----|----------|----------|-------------|----------|---------------|
| ITP-C01 | CRITICAL | Missing Example | No complete end-to-end example of Test Execution Pattern | Phase 4 | Add "Example: Testing Login Button" showing all 8 steps with actual tool calls and expected outputs |
| ITP-C02 | MAJOR | Edge Case | No handling for screenshot capture failure | Phase 4 | Add: "If screenshot fails: retry once, then continue test and log 'SCREENSHOT_UNAVAILABLE' in notes" |
| ITP-C03 | MAJOR | Edge Case | No handling for describe_ui returning empty/minimal tree | Phase 4 | Add: "If describe_ui returns <5 elements: verify app is in foreground, retry after 1s, log if persists" |
| ITP-C04 | MAJOR | Missing Context | No iOS version requirements or compatibility notes | Preconditions | Add: "Minimum iOS version: 15.0 (or match app's deployment target)" |
| ITP-C05 | CRITICAL | Missing Context | No explanation of what "test flow registry" is or where to find it | Specific Flow definition | Define: "Test flow registry is a document at `knowledge/qa/test-flows.md` containing named flows with step-by-step actions" |
| ITP-C06 | MAJOR | Edge Case | No handling for network timeout during API-dependent tests | Phase 4 | Add: "Network timeout (>10s): mark test as SKIPPED, note 'NETWORK_TIMEOUT', continue with next test" |
| ITP-C07 | MINOR | Missing Example | No example of a failed test documentation | Report section | Add example entry for Issues Found table with realistic failure |
| ITP-C08 | MAJOR | Missing Context | No guidance on when to use coordinates vs labels for tap | MCP Tools Reference | Add: "Prefer label when available (more stable); use coordinates only when no accessibility label exists" |
| ITP-C09 | MINOR | Edge Case | No handling for app crash during testing | Phase 4 | Add: "App crash: capture crash log if available, restart app, retry current test once, then mark FAIL" |
| ITP-C10 | MAJOR | Missing Context | No explanation of severity criteria (HIGH/MEDIUM/LOW) | Report section | Add definitions: "HIGH: Blocks core functionality, MEDIUM: Degrades UX but workaround exists, LOW: Minor polish issue" |
| ITP-C11 | MINOR | Missing Example | Visual Inspection Checklist items lack pass/fail examples | Visual Inspection Checklist | Add one example per category showing what PASS vs FAIL looks like |
| ITP-C12 | MAJOR | Edge Case | "Maximum ~15 screenshots per session" - what happens at limit? | Constraints | Add: "At 15 screenshot limit: save current batch, note truncation, prioritize remaining critical states" |
| ITP-C13 | MINOR | Missing Context | No guidance on Metro bundler error interpretation | Troubleshooting | Add common Metro errors and their meanings |
| ITP-C14 | MAJOR | Edge Case | No handling for build warning vs error distinction | Gate 2 | Clarify: "Warnings do not block Gate 2; errors require resolution" |

### Clarity Assessment
- **Overall**: MEDIUM - Strong base but significant gaps in edge case handling
- **Implicit assumptions found**:
  - User knows how to interpret build output (warnings vs errors)
  - User knows iOS simulator behavior quirks
  - User knows how to handle app state between tests
  - User knows what constitutes a "critical path" for smoke tests
- **Vague terms requiring definition**: Listed in other analyst reports

### Precision Problems
| Vague Phrase | Location | Precise Alternative |
|--------------|----------|---------------------|
| N/A - completeness focus | | |

### Ambiguity Issues
- **Conflicting instructions**: None found
- **Undefined terms**: See ITP-C05 (test flow registry)
- **Scope uncertainties**: What happens when constraints are hit (screenshot limit, session timeout)?

### Missing Context
- **Missing prerequisites**:
  - iOS version requirements
  - Minimum Xcode version
  - Required disk space for screenshots
  - Network requirements for API testing
- **Missing examples**:
  - Complete 8-step Test Execution Pattern walkthrough
  - Failed test documentation example
  - Visual Inspection pass/fail examples
- **Missing edge case handling**:
  - Screenshot failure
  - Empty UI tree
  - Network timeout
  - App crash
  - Session timeout
  - Screenshot limit reached
  - Build warnings (vs errors)

### Structure Assessment
- **Scannability**: HIGH
- **Prose vs structured ratio**: Good
- **Recommendations**: Add "Edge Cases" subsection to Phase 4

### Condensation Opportunities
| Original | Condensed | Tokens Saved |
|----------|-----------|--------------|
| N/A - completeness focus, should add content not remove | | |

### Critical Missing Section: Edge Case Handling

Recommend adding this section to Phase 4:

```markdown
### Edge Case Handling

| Situation | Detection | Action | Log Entry |
|-----------|-----------|--------|-----------|
| Screenshot fails | Tool returns error | Retry once, continue | "SCREENSHOT_UNAVAILABLE at {step}" |
| describe_ui empty | <5 elements returned | Wait 1s, retry, log | "UI_TREE_MINIMAL at {step}" |
| Network timeout | >10s API response | Mark SKIPPED | "NETWORK_TIMEOUT: {endpoint}" |
| App crash | Process terminated | Restart, retry once | "APP_CRASH at {step}, retried: {pass/fail}" |
| Build warnings | Warning in output | Continue (not blocking) | N/A (warnings suppressed) |
| Screenshot limit | 15 reached | Save batch, continue | "SCREENSHOT_LIMIT: prioritizing {remaining}" |
| Session timeout | Pipeline exceeds 30min | Exit PARTIAL | "SESSION_TIMEOUT at Phase {n}" |
```

### Cross-File Observations
- Edge case handling patterns should be consistent across all testing pipelines
- The "test flow registry" concept may need a dedicated document defining its structure
- Severity definitions (HIGH/MEDIUM/LOW) should be standardized across all QA documentation
