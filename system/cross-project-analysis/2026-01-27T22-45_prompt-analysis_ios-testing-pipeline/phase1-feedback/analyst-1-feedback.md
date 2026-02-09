# Prompt Analysis Feedback - Analyst 1

**Focus Area**: Clarity and Precision (vague terms, implicit assumptions, hedged language)
**Documents Analyzed**: `ios-testing-pipeline.md`
**Date**: 2026-01-27T22:45:00Z

## ios-testing-pipeline.md

### Strengths
- Clear pipeline overview diagram with visual flow
- Well-structured phase breakdown
- Includes concrete code examples for MCP tool calls
- Defined exit states with clear criteria
- Screenshot naming convention is explicit
- Troubleshooting table provides specific resolutions

### Issues

| ID | Severity | Category | Description | Location | Suggested Fix |
|----|----------|----------|-------------|----------|---------------|
| ITP-001 | MAJOR | Precision | "auto-detect" for simulator and app_path lacks specificity on detection method | Parameters table | Replace "auto-detect" with explicit detection sequence: "Uses `list_sims()` to find first booted simulator, or boots iPhone 16 Pro if none available" |
| ITP-002 | MAJOR | Precision | "Backend services running (if app requires API)" is vague | Preconditions | Specify: "Backend API reachable at configured BASE_URL (verify with health check endpoint)" |
| ITP-003 | MINOR | Precision | "300-500ms" wait range is imprecise | Test Execution Pattern | Specify: "Use 400ms as default; reduce to 300ms for simple state changes, increase to 500ms for network-dependent transitions" |
| ITP-004 | MAJOR | Implicit | "app's test flow registry" is referenced but never defined | Specific Flow definition | Add: "Test flow registry: Document listing all testable flows at `knowledge/qa/test-flows.md` or define inline in test parameters" |
| ITP-005 | MINOR | Precision | "appropriate" used in font description checklist | Visual Inspection Checklist | Replace "Font sizes and weights look appropriate" with "Font sizes are readable (min 11pt) and weights match design spec" |
| ITP-006 | MAJOR | Implicit | "properly" in alignment check is subjective | Visual Inspection Checklist | Replace "Text and elements align correctly" with "Text and elements align to a consistent grid; no elements offset by 1-2 pixels from their neighbors" |
| ITP-007 | MINOR | Precision | "smooth, not jarring" for transitions is subjective | Navigation & Flow checklist | Replace with: "Transitions complete within 300ms without flicker or jump cuts" |
| ITP-008 | CRITICAL | Implicit | No definition of what constitutes a "state change" requiring screenshot | Test Execution Pattern | Add explicit definition: "State change = any UI modification visible to user: screen navigation, modal open/close, loading state, form field change, error display" |
| ITP-009 | MAJOR | Precision | "Aesthetically poor design choices" is entirely subjective | UX Issues to Flag | Replace with specific criteria: "Inconsistent color usage, mismatched component styles, or visual elements that contradict iOS HIG" |
| ITP-010 | MINOR | Hedged | "if applicable" used multiple times without criteria | Various locations | Replace each instance with explicit conditions: "if using CocoaPods (check for Podfile existence)" |

### Clarity Assessment
- **Overall**: MEDIUM
- **Implicit assumptions found**:
  - Assumes knowledge of iOS development terminology (CocoaPods, Metro bundler)
  - Assumes bundle ID extraction process is understood
  - Assumes test flow registry exists without defining structure
  - Assumes "state change" concept is universally understood
- **Vague terms requiring definition**:
  - "auto-detect" (needs algorithm)
  - "appropriate" (needs criteria)
  - "properly" (needs measurable standard)
  - "smooth" (needs timing constraint)
  - "jarring" (needs anti-pattern examples)
  - "ugly" (needs objective criteria)

### Precision Problems
| Vague Phrase | Location | Precise Alternative |
|--------------|----------|---------------------|
| "look uniform" | Spacing check | "spacing values differ by no more than 4px from design spec or component siblings" |
| "look tappable" | Interactive Elements | "have minimum 44x44pt touch target, distinct visual styling from non-interactive elements" |
| "when expected" | Keyboard dismiss | "on tap outside text field, on submit action, or on explicit dismiss gesture" |
| "weird placement" | UX Issues | "elements positioned outside standard iOS layout zones or inconsistent with similar screens" |
| "15-30 minutes" | Full Test scope | specify exact timeout: "30 minute maximum; escalate to PARTIAL if incomplete" |

### Ambiguity Issues
- **Conflicting instructions**: None found
- **Undefined terms**: "test flow registry", "state change", "visual affordance"
- **Scope uncertainties**: Unclear if Visual Inspection Checklist applies to EVERY screen or only key screens

### Missing Context
- **Missing prerequisites**: No mention of required iOS version compatibility
- **Missing examples**: No example of a complete test flow execution showing all 8 steps
- **Missing edge case handling**: What if screenshot capture fails? What if describe_ui returns empty?

### Structure Assessment
- **Scannability**: HIGH - Good use of headers, tables, and code blocks
- **Prose vs structured ratio**: 20% prose / 80% structured - appropriate balance
- **Recommendations**: None - structure is well-organized

### Condensation Opportunities
| Original | Condensed | Tokens Saved |
|----------|-----------|--------------|
| "Screenshot name: `01_initial_launch.png`" | Move to naming convention section, remove duplication | ~8 |
| "This is non-negotiable" after CRITICAL notice | Redundant with CRITICAL label | ~4 |
| "At each screen/state, verify ALL of the following:" | "Verify at each screen:" | ~6 |

### Cross-File Observations
- The precision issues around subjective terms ("appropriate", "ugly", "weird") likely appear in other QA-related documents
- The "auto-detect" pattern without algorithm definition may be a common pattern in other pipelines
