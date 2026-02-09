# Prompt Analysis Feedback - Analyst 4

**Focus Area**: Ambiguity Detection (conflicting instructions, undefined terms, multiple interpretations)
**Documents Analyzed**: `ios-testing-pipeline.md`
**Date**: 2026-01-27T22:45:00Z

## ios-testing-pipeline.md

### Strengths
- Clear gate criteria at each phase boundary
- Explicit MCP tool call syntax reduces ambiguity
- Screenshot naming convention has unambiguous pattern
- Exit states are clearly enumerated
- Phase objectives stated at start of each phase

### Issues

| ID | Severity | Category | Description | Location | Suggested Fix |
|----|----------|----------|-------------|----------|---------------|
| ITP-A01 | CRITICAL | Undefined Term | "state change" used as trigger for screenshots but never defined | Phase 4, Test Execution Pattern | Define: "State change: Any visible UI modification including navigation, modal display, loading indicators, content updates, or input field changes" |
| ITP-A02 | MAJOR | Conflicting | Exit States defined in overview says "Tests Done" but Gate 4 says "Testing Complete" | Overview diagram vs Gate 4 | Standardize to "Tests Complete" in both locations |
| ITP-A03 | MAJOR | Multiple Interpretations | "Visual Inspection Checklist applied to every screen" - does this mean every screenshot or every unique screen? | Gate 4 | Clarify: "applied to every unique screen (not every screenshot of same screen)" |
| ITP-A04 | MAJOR | Undefined Term | "test flow registry" referenced without definition | Test Scope Definitions | Add definition and location: "Defined in `knowledge/qa/test-flows.md` or passed as parameter" |
| ITP-A05 | MINOR | Multiple Interpretations | "if app requires API" - who determines this and how? | Preconditions | Clarify: "Check if app makes network requests during smoke test flows" |
| ITP-A06 | CRITICAL | Ambiguity | "EVERY state change" - could mean literally every pixel change or meaningful UI changes | CRITICAL notice in Phase 4 | Enumerate what counts: "navigation, modal open/close, loading state appearance/disappearance, form field changes, error/success messages" |
| ITP-A07 | MAJOR | Undefined Term | "visual affordance" used in checklist | Interactive Elements checklist | Define: "Visual indication element is tappable (e.g., button styling, underlined text, chevron icons)" |
| ITP-A08 | MINOR | Multiple Interpretations | "properly" in "align properly" | Visual Inspection Checklist | Remove subjective term: "align to consistent grid" |
| ITP-A09 | MAJOR | Conflicting | Test Execution Pattern shows 8 steps, but "VERIFY" and "INSPECT" could overlap | Test Execution Pattern | Clarify distinction: "VERIFY = check expected elements exist; INSPECT = run Visual Inspection Checklist quality check" |
| ITP-A10 | MINOR | Undefined Term | "critical path" in smoke test definition | Test Scope Definitions | Define: "core user journey required for app to provide primary value (e.g., login -> main feature)" |
| ITP-A11 | MAJOR | Multiple Interpretations | "attempt restart once" - restart what? App? Simulator? Phase? | Gate 3 Failure Action | Specify: "restart app via `stop_app_sim` then `launch_app_sim`" |
| ITP-A12 | MINOR | Ambiguity | "correct naming" for screenshots in Gate 5 | Gate 5 | Reference specific naming convention or provide validation pattern |
| ITP-A13 | MAJOR | Undefined Term | "key elements verified" in Screens Verified table | Report Template | Clarify what constitutes "key elements" or provide examples |
| ITP-A14 | MINOR | Multiple Interpretations | "smoke test items" in Full Test scope | Test Scope Definitions | Either embed smoke test items or say "items 1-4 from Smoke Test scope" |

### Clarity Assessment
- **Overall**: MEDIUM - Some critical terms lack definition
- **Implicit assumptions found**:
  - "state change" is universally understood
  - VERIFY vs INSPECT distinction is clear
  - "restart" scope is obvious
  - "key elements" is self-explanatory
- **Vague terms requiring definition**:
  - state change
  - visual affordance
  - critical path
  - key elements
  - test flow registry

### Precision Problems
| Vague Phrase | Location | Precise Alternative |
|--------------|----------|---------------------|
| "state change" | Test Execution Pattern | Explicit enumeration of qualifying changes |
| "visual affordance" | Interactive Elements | "visual indication of interactivity" |
| "properly" | Alignment check | "to consistent grid without visible offset" |
| "key elements" | Report template | "primary interactive and content elements" |

### Ambiguity Issues
- **Conflicting instructions**:
  - "Tests Done" vs "Testing Complete" terminology
  - VERIFY vs INSPECT step distinction unclear
- **Undefined terms**:
  - state change (critical - determines screenshot frequency)
  - test flow registry (critical - affects Specific Flow tests)
  - visual affordance
  - critical path
  - key elements
- **Scope uncertainties**:
  - What exactly triggers a required screenshot?
  - Does Visual Inspection apply to every screenshot or every unique screen?
  - What constitutes a "restart" in failure recovery?

### Missing Context
- **Missing prerequisites**: Definition glossary
- **Missing examples**: Borderline cases for "state change"
- **Missing edge case handling**: Ambiguous state changes (loading spinners appearing/disappearing rapidly)

### Structure Assessment
- **Scannability**: HIGH
- **Prose vs structured ratio**: Good
- **Recommendations**: Add "Definitions" section at top

### Proposed Definitions Section

```markdown
## Key Definitions

| Term | Definition |
|------|------------|
| **State change** | Any visible UI modification: screen navigation, modal open/close, loading indicator appear/disappear, form field value change, error/success message display, content update |
| **Visual affordance** | Visual indication that element is interactive (button styling, underlined text, chevron icon, color/opacity change on press) |
| **Critical path** | Minimum user journey for app to deliver primary value (typically: launch -> auth -> main feature access) |
| **Test flow registry** | Document at `knowledge/qa/test-flows.md` defining named test flows with step-by-step actions |
| **Key elements** | Primary interactive elements (buttons, inputs) and content elements (titles, main text) that define screen purpose |
```

### Condensation Opportunities
| Original | Condensed | Tokens Saved |
|----------|-----------|--------------|
| N/A - ambiguity focus | | |

### Cross-File Observations
- The "state change" definition is critical for consistent test coverage across different testers/agents
- Terminology inconsistency ("Tests Done" vs "Testing Complete") may exist in other pipelines
- The VERIFY/INSPECT distinction pattern may need clarification in other testing documentation
