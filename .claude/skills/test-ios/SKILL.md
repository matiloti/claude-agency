# iOS Testing Skill

> Owner: QA Tester / Frontend Developer | Version: 2.0 | Last Updated: 2026-01-27

Comprehensive automated iOS testing using Xcode MCP tools. Builds, deploys, and tests React Native apps on iOS simulators with structured reporting.

**Pipeline Reference**: This skill implements the [iOS Testing Pipeline](../../../system/orchestration/pipelines/ios-testing-pipeline.md). For the complete process definition with Visual Inspection Checklist, see the pipeline documentation.

## Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `test_scope` | No | `full` | Test scope: `full` (all flows), `smoke` (critical path only), or specific flow name |
| `simulator` | No | auto-detect | Simulator name (e.g., "iPhone 16 Pro"). Uses booted or first available if not specified |
| `app_path` | No | auto-detect | Workspace path. Auto-discovers `.xcworkspace` in `ios/` or project root |
| `create_google_doc` | No | `true` | Whether to create a Google Docs version of the report |

## Preconditions

- [ ] Xcode installed and configured
- [ ] XcodeBuildMCP server available (verify with `mcp__XcodeBuildMCP__doctor`)
- [ ] iOS simulator available (`mcp__XcodeBuildMCP__list_sims`)
- [ ] Backend services running (if app requires API)
- [ ] Metro bundler running (for React Native apps)

## Exit States

| State | Description | Next Action |
|-------|-------------|-------------|
| **Success** | All tests pass, report generated, Google Doc created (if enabled) | Proceed with development/release |
| **Partial** | Some tests fail, issues logged | Create tasks for HIGH/MEDIUM issues |
| **Blocked** | Cannot build or deploy | Escalate to orchestrator with error details |

---

## Target Users

This skill is designed for:

| Role | Use Case |
|------|----------|
| **Frontend Developer** | Pre-review testing before submitting for Judge review |
| **QA Tester** | Comprehensive E2E test execution |
| **CI Agent** | Automated verification in continuous integration |

---

## Workflow Phases

### Phase 1: Setup

Configure Xcode session defaults for the test run.

**Steps:**

1. **Discover project files**
   ```
   mcp__XcodeBuildMCP__discover_projs({ workspaceRoot: "/path/to/project" })
   ```

2. **List available simulators**
   ```
   mcp__XcodeBuildMCP__list_sims()
   ```

3. **Set session defaults**
   ```
   mcp__XcodeBuildMCP__session-set-defaults({
     workspacePath: "/path/to/ios/App.xcworkspace",
     scheme: "AppName",
     simulatorName: "iPhone 16 Pro",
     useLatestOS: true,
     suppressWarnings: true
   })
   ```

4. **Boot simulator if needed**
   ```
   mcp__XcodeBuildMCP__boot_sim()
   mcp__XcodeBuildMCP__open_sim()
   ```

**Checklist:**
- [ ] Workspace path resolved
- [ ] Scheme identified
- [ ] Simulator selected and booted
- [ ] Session defaults configured

---

### Phase 2: Build

Build the app for the iOS simulator.

**Steps:**

1. **Check if CocoaPods needed** (React Native projects)
   ```bash
   cd ios && pod install
   ```

2. **Build for simulator**
   ```
   mcp__XcodeBuildMCP__build_sim()
   ```

   Or build and run in one step:
   ```
   mcp__XcodeBuildMCP__build_run_sim()
   ```

**Troubleshooting:**

| Issue | Resolution |
|-------|------------|
| Pod install fails | Run `pod repo update` then retry |
| Build fails with signing error | Check scheme uses "Debug" configuration |
| Metro bundler not found | Start Metro with `npx react-native start --reset-cache` |
| Derived data issues | Run `mcp__XcodeBuildMCP__clean()` |

**Checklist:**
- [ ] Pod dependencies installed (if applicable)
- [ ] Build completed successfully
- [ ] No critical warnings

---

### Phase 3: Deploy

Install and launch the app on the simulator.

**Steps:**

1. **Get the built app path**
   ```
   mcp__XcodeBuildMCP__get_sim_app_path({ platform: "iOS Simulator" })
   ```

2. **Install the app**
   ```
   mcp__XcodeBuildMCP__install_app_sim({ appPath: "/path/to/App.app" })
   ```

3. **Get the bundle ID**
   ```
   mcp__XcodeBuildMCP__get_app_bundle_id({ appPath: "/path/to/App.app" })
   ```

4. **Launch the app**
   ```
   mcp__XcodeBuildMCP__launch_app_sim({ bundleId: "com.example.app" })
   ```

5. **Take initial screenshot to verify launch**
   ```
   mcp__XcodeBuildMCP__screenshot()
   ```

**Checklist:**
- [ ] App installed on simulator
- [ ] App launched successfully
- [ ] Initial screen visible

---

### Phase 4: Test

Execute flow tests using UI automation.

**Core Testing Tools:**

| Tool | Purpose | Example |
|------|---------|---------|
| `screenshot` | Capture visual state | `mcp__XcodeBuildMCP__screenshot()` |
| `describe_ui` | Get element coordinates/accessibility tree | `mcp__XcodeBuildMCP__describe_ui()` |
| `tap` | Tap element by coordinates, id, or label | `mcp__XcodeBuildMCP__tap({ label: "Login" })` |
| `type_text` | Enter text in focused field | `mcp__XcodeBuildMCP__type_text({ text: "user@example.com" })` |
| `gesture` | Scroll, swipe gestures | `mcp__XcodeBuildMCP__gesture({ preset: "scroll-down" })` |
| `key_press` | Press keyboard keys | `mcp__XcodeBuildMCP__key_press({ keyCode: 40 })` (Return) |
| `swipe` | Custom swipe between coordinates | `mcp__XcodeBuildMCP__swipe({ x1, y1, x2, y2 })` |
| `long_press` | Long press at coordinates | `mcp__XcodeBuildMCP__long_press({ x, y, duration: 1000 })` |

**Testing Pattern:**

For each test case:

1. **Describe UI** to understand current screen state
2. **Execute action** (tap, type, swipe)
3. **Wait briefly** for UI to update (use `postDelay` parameter)
4. **Screenshot** to capture result
5. **Verify** expected elements are present
6. **Log result** (PASS/FAIL with notes)

**Example Test Flow:**

```
// Test: User Login
1. describe_ui() → Find email field
2. tap({ label: "Email" }) → Focus email field
3. type_text({ text: "test@example.com" })
4. tap({ label: "Password" }) → Focus password field
5. type_text({ text: "password123" })
6. tap({ label: "Login" }) → Submit form
7. screenshot() → Capture result
8. describe_ui() → Verify home screen elements present
```

**Test Scope Guidelines:**

| Scope | Description | Typical Duration |
|-------|-------------|------------------|
| `smoke` | Critical path only (auth, main feature) | 5-10 minutes |
| `full` | All user flows | 15-30 minutes |
| `specific-flow` | Single flow (e.g., "authentication") | 3-5 minutes |

**Common Key Codes:**

| Key | Code |
|-----|------|
| Return/Enter | 40 |
| Backspace | 42 |
| Tab | 43 |
| Space | 44 |

**Checklist:**
- [ ] All flows in scope executed
- [ ] Screenshots captured for each major state
- [ ] Issues documented with reproduction steps

---

### Phase 5: Report

Generate a structured test report with all findings.

**Report Location:**

```
knowledge/qa/test-reports/
  {YYYY-MM-DD}T{HH-MM}_{test-name}/
    report.md              # Main test report
    screenshots/           # All screenshots (MUST save)
```

**Filename Convention:**
- Format: `{timestamp}_{descriptive-slug}`
- Example: `2026-01-27T14-30_auth-flow-test`

**Create the report folder:**
```bash
mkdir -p knowledge/qa/test-reports/{timestamp}_{test-name}/screenshots
```

---

## Report Template

Use this template for the test report:

```markdown
# {App Name} - {Test Name} Report

**Date**: {YYYY-MM-DD}
**Test Type**: {Smoke Test / Full E2E / Specific Flow}
**Platform**: iOS Simulator ({device name})
**Tester**: {Agent name}
**Screenshots**: See `screenshots/` folder

---

## Executive Summary

{1-2 paragraph summary of test results and key findings}

**Overall Pass Rate: {X}% ({passed}/{total} tests)**

---

## Test Environment

| Component | Details |
|-----------|---------|
| **Xcode Version** | {version} |
| **Simulator** | {device name} ({UUID}) |
| **iOS Version** | iOS {version} |
| **Bundle ID** | `{bundle.id}` |
| **Workspace** | `{workspace.xcworkspace}` |
| **Build Configuration** | Debug |
| **Backend** | {URL or N/A} |

---

## Build Status

| Item | Status |
|------|--------|
| **Xcode Build** | {SUCCESS/FAIL} |
| **Pod Install** | {SUCCESS/FAIL/N/A} |
| **App Launch** | {SUCCESS/FAIL} |

---

## Test Results by Category

### {Category 1: e.g., Authentication Flows}

| Test Case | Status | Notes |
|-----------|--------|-------|
| {Test name} | {PASS/FAIL/PARTIAL} | {Brief notes} |

### {Category 2: e.g., Navigation Flows}

| Test Case | Status | Notes |
|-----------|--------|-------|
| {Test name} | {PASS/FAIL/PARTIAL} | {Brief notes} |

{Add more categories as needed}

---

## Issues Found

### HIGH Priority

| ID | Issue | Location | Expected | Actual |
|----|-------|----------|----------|--------|
| BUG-001 | {Description} | {Screen/Component} | {Expected behavior} | {Actual behavior} |

### MEDIUM Priority

| ID | Issue | Location | Expected | Actual |
|----|-------|----------|----------|--------|
| BUG-002 | {Description} | {Screen/Component} | {Expected behavior} | {Actual behavior} |

### LOW Priority

| ID | Issue | Location | Expected | Actual |
|----|-------|----------|----------|--------|
| UI-001 | {Description} | {Screen/Component} | {Expected behavior} | {Actual behavior} |

---

## Screens Verified

1. **{Screen Name}** - {Key elements verified}
2. **{Screen Name}** - {Key elements verified}
{Continue for all screens}

---

## Recommendations

### Immediate Fixes Required

1. **{BUG-ID}**: {Specific fix recommendation}

### Improvements Suggested

1. {Improvement suggestion}

---

## Test Execution Details

### Tools Used

- `mcp__XcodeBuildMCP__build_run_sim` - Build and launch app
- `mcp__XcodeBuildMCP__screenshot` - Capture screenshots
- `mcp__XcodeBuildMCP__describe_ui` - Get accessibility tree
- `mcp__XcodeBuildMCP__tap` - Simulate taps
- `mcp__XcodeBuildMCP__type_text` - Enter text inputalso
- `mcp__XcodeBuildMCP__gesture` - Scroll and swipe

### Test Duration

- Setup & Build: ~{X} minutes
- Flow Testing: ~{X} minutes
- Report Generation: ~{X} minutes

---

## Conclusion

{Final assessment and next steps}

---

*Report generated via /test-ios workflow*
```

### Google Docs Report Generation

After creating the markdown report, generate a formatted Google Docs version:

**Prerequisites:**
- Google Docs MCP server configured
- Report markdown file created at `knowledge/qa/test-reports/{folder}/report.md`

**Steps:**

1. **Spawn Google Docs Writer agent** with the report path:
   ```
   Task({
     subagent_type: "general-purpose",
     prompt: [Use google-docs-writer prompt template with:
       - markdown_path: path to report.md
       - screenshots_path: path to screenshots/ folder
       - doc_title: "{App Name} - Test Report - {date}"]
   })
   ```

2. **Capture the Google Docs URL** from the agent's response

3. **Update the markdown report** to include the Google Docs link:
   ```markdown
   ---

   **Google Docs Version**: [View Formatted Report]({google_docs_url})
   ```

**Skip Condition:**
- If `create_google_doc` parameter is `false`, skip this step
- If Google Docs MCP server is unavailable, log warning and continue

---

## Post-Test Actions

After generating the report:

### 1. Create Tasks for Issues

For each HIGH or MEDIUM priority issue found:

```
TaskCreate({
  subject: "Fix {issue description}",
  description: "From test report {path}: {issue details}",
  activeForm: "Fixing {issue}"
})
```

### 2. Update Project Status

Add test summary to `knowledge/project-status.md`:

```markdown
## Recent Test Results

- **Date**: {timestamp}
- **Pass Rate**: {X}%
- **Issues Found**: {count} (HIGH: {n}, MEDIUM: {n}, LOW: {n})
- **Report**: `knowledge/qa/test-reports/{folder}/report.md`
```

### 3. Screenshot Note

**Important**: Screenshots captured via `mcp__XcodeBuildMCP__screenshot()` are displayed inline during the session but are ephemeral. To preserve screenshots:

- Take screenshots manually using Simulator > File > Save Screen
- Use screen recording: `mcp__XcodeBuildMCP__record_sim_video()`
- Document screenshot descriptions in the report for future reference

### 4. Google Docs Report

If `create_google_doc` is enabled:
- A formatted Google Doc is created with:
  - Professional styling (headings, tables, lists)
  - Embedded screenshots
  - Color-coded status indicators (PASS/FAIL)
- The Google Docs URL is added to the markdown report
- Share the Google Docs URL in the completion summary

---

## MCP Tools Reference

### Session Management

| Tool | Description |
|------|-------------|
| `session-set-defaults` | Configure workspace, scheme, simulator for session |
| `session-show-defaults` | Display current session configuration |
| `session-clear-defaults` | Reset session configuration |

### Project Discovery

| Tool | Description |
|------|-------------|
| `discover_projs` | Find Xcode projects and workspaces in directory |
| `list_schemes` | List available build schemes |
| `show_build_settings` | Display xcodebuild settings |

### Simulator Management

| Tool | Description |
|------|-------------|
| `list_sims` | List available iOS simulators |
| `boot_sim` | Boot the configured simulator |
| `open_sim` | Open Simulator app window |
| `erase_sims` | Reset simulator to clean state |
| `set_sim_appearance` | Set dark/light mode |
| `set_sim_location` | Set GPS coordinates |

### Build & Deploy

| Tool | Description |
|------|-------------|
| `build_sim` | Build app for simulator |
| `build_run_sim` | Build and launch app |
| `install_app_sim` | Install app bundle on simulator |
| `launch_app_sim` | Launch app by bundle ID |
| `stop_app_sim` | Stop running app |
| `get_sim_app_path` | Get path to built app |
| `get_app_bundle_id` | Extract bundle ID from app |
| `clean` | Clean build products |

### UI Automation

| Tool | Description |
|------|-------------|
| `describe_ui` | Get view hierarchy with coordinates |
| `screenshot` | Capture simulator screenshot |
| `tap` | Tap at coordinates or element |
| `type_text` | Type text in focused field |
| `gesture` | Preset gestures (scroll, swipe) |
| `swipe` | Custom swipe between points |
| `long_press` | Long press at coordinates |
| `key_press` | Press single key by keycode |
| `key_sequence` | Press multiple keys |
| `button` | Press hardware button (home, lock) |
| `touch` | Touch down/up events |

### Logging & Recording

| Tool | Description |
|------|-------------|
| `start_sim_log_cap` | Start capturing app logs |
| `stop_sim_log_cap` | Stop and retrieve logs |
| `record_sim_video` | Start/stop video recording |

### Diagnostics

| Tool | Description |
|------|-------------|
| `doctor` | Check MCP server environment |

### Google Docs Integration

| Tool | Description |
|------|-------------|
| `google-docs-writer` agent | Converts markdown report to formatted Google Doc |

**Note**: Requires Google Docs MCP server to be configured. The agent handles:
- Markdown parsing and conversion
- Image embedding from screenshots folder
- Professional formatting and styling
- Status color coding

---

## Related Skills

- **`/ios-simulator-skill-xcode`** - Basic iOS simulator operations
- **`/expo-workflow`** - Expo-specific development workflow

---

## Examples

### Smoke Test Invocation

```
Run /test-ios with scope=smoke to verify critical paths work
```

### Full Test for Specific Simulator

```
Run /test-ios on iPhone 15 Pro Max simulator with full test coverage
```

### Pre-Review Testing

```
Before submitting for review, run /test-ios smoke test and fix any HIGH priority issues
```

### Full Test with Google Docs Report

```
Run /test-ios with full coverage and generate Google Docs report for sharing with stakeholders
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Simulator won't boot | Run `xcrun simctl shutdown all` then retry |
| App crashes on launch | Check Metro bundler is running, try `--reset-cache` |
| UI elements not found | Use `describe_ui` to see actual element labels |
| Tap not registering | Verify coordinates with `describe_ui`, add `postDelay` |
| Build takes too long | Enable `suppressWarnings: true` in session defaults |
| Cannot find workspace | Verify path with `discover_projs` |

---

## Constraints

- Maximum ~15 screenshots per session (MCP limitation)
- Screenshots are ephemeral unless manually saved
- Test duration limited by session timeout
- Cannot test physical device interactions (Face ID, etc.)
- Network-dependent tests require backend services running
