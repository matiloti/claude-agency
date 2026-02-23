# iOS Testing Pipeline

<!-- Last Updated: 2026-02-23 -->
<!-- Status: Current -->
<!-- Author: workflow-expert -->

> Owner: QA Tester / Frontend Developer | Version: 2.4

A linear, staged pipeline for comprehensive iOS app testing using Xcode MCP tools. This pipeline builds, deploys, tests, and reports on React Native iOS apps with emphasis on visual/UX quality detection.

---

## Pipeline Overview

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                              iOS Testing Pipeline                                  │
├───────────────────────────────────────────────────────────────────────────────────┤
│                                                                                    │
│ ┌───────┐   ┌───────┐   ┌───────┐   ┌───────┐   ┌───────┐   ┌───────┐            │
│ │ PORTS │──►│ SETUP │──►│ BUILD │──►│DEPLOY │──►│ TEST  │──►│REPORT │            │
│ └───────┘   └───────┘   └───────┘   └───────┘   └───────┘   └───────┘            │
│     │           │           │           │           │           │                 │
│     ▼           ▼           ▼           ▼           ▼           ▼                 │
│ [Gate 0]    [Gate 1]    [Gate 2]    [Gate 3]    [Gate 4]    [Gate 5]             │
│ Env Ready   Session OK  Build OK    App Running Tests Complete  Report OK        │
│                                                                                    │
└───────────────────────────────────────────────────────────────────────────────────┘

Exit States:
  ✓ SUCCESS  - All tests pass, report generated
  ◐ PARTIAL  - Some tests fail, issues documented
  ✗ BLOCKED  - Cannot build/deploy, escalate with details
```

---

## Table of Contents

- [Parameters](#parameters)
- [Key Definitions](#key-definitions)
- [Preconditions](#preconditions)
- [Phase 0: Port & Connectivity Validation](#phase-0-port--connectivity-validation)
- [Phase 1: Setup](#phase-1-setup)
- [Phase 2: Build](#phase-2-build)
- [Phase 3: Deploy](#phase-3-deploy)
- [Phase 4: Test](#phase-4-test)
- [Phase 5: Report](#phase-5-report)
- [Troubleshooting Reference](#troubleshooting-reference)
- [Constraints](#constraints)

---

## Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `test_scope` | No | `full` | Test scope: `full`, `smoke`, or specific flow name |
| `simulator` | No | auto-detect | Simulator name (e.g., "iPhone 16 Pro") |
| `app_path` | No | auto-detect | Path to `.xcworkspace` |
| `create_google_doc` | No | `true` | Generate Google Docs report |
| `reset_simulator` | No | `false` | Erase simulator before testing |

---

## Key Definitions

| Term | Definition |
|------|------------|
| **State change** | Any visible UI modification including: screen navigation, modal/sheet open or close, loading indicator appear or disappear, form field value or focus change, error or success message display, list content update, tab/segment selection change |
| **Visual affordance** | Visual indication element is interactive: button styling (background, border, shadow), underlined text for links, or system-standard icons (chevron, plus, x) |
| **Critical path** | Minimum user journey for app to deliver primary value (typically: launch -> auth -> main feature) |
| **Test flow registry** | Document at `knowledge/qa/test-flows.md` defining named test flows with step-by-step actions |

---

## Preconditions

Before starting this pipeline:

- [ ] Xcode installed and configured
- [ ] XcodeBuildMCP server available (`mcp__XcodeBuildMCP__doctor`)
- [ ] XcodeBuildMCP v1.x or compatible version
- [ ] iOS simulator available (`mcp__XcodeBuildMCP__list_sims`)
- [ ] Backend services running (if app requires API)
- [ ] Metro bundler running (`npx react-native start`)

---

## Phase 0: Port & Connectivity Validation

**Objective**: Ensure backend and frontend ports are available and correctly configured.

> **Why**: Multiple projects may run on this machine. Port conflicts will cause silent failures, connect to wrong backends, or prevent Metro bundler from starting.

### Steps

1. **Check backend port availability**
   ```bash
   lsof -i :{BACKEND_PORT} | grep LISTEN
   ```
   - If port is in use by another process → find next available port
   - If port is in use by our backend → proceed

2. **If port conflict, reassign**
   ```bash
   # Find available port (starting from configured +1)
   for port in $(seq {BACKEND_PORT+1} {BACKEND_PORT+10}); do
     lsof -i :$port | grep LISTEN || { echo "Available: $port"; break; }
   done
   ```
   - Update backend configuration with new port
   - Restart backend on new port

3. **Verify frontend API configuration**
   - Check frontend's API base URL matches backend port
   - Update if mismatched (e.g., `.env`, config file)

4. **Confirm backend connectivity**
   ```bash
   curl -s http://localhost:{BACKEND_PORT}/health || echo "FAIL"
   ```

5. **Check frontend port availability (Metro bundler)**
   ```bash
   lsof -i :8081 | grep LISTEN
   ```
   - If port is in use by another process -> find next available port
   - If port is in use by our Metro bundler -> proceed

6. **If frontend port conflict, reassign**
   ```bash
   # Find available port (starting from 8082)
   for port in $(seq 8082 8091); do
     lsof -i :$port | grep LISTEN || { echo "Available: $port"; break; }
   done
   ```
   - Start Metro with alternate port: `npx react-native start --port {NEW_PORT}`

7. **Verify Metro bundler is running**
   ```bash
   curl -s http://localhost:{METRO_PORT}/status | grep "packager-status:running" || echo "FAIL"
   ```

8. **Confirm frontend-backend connection**
   - Verify frontend's API base URL matches backend port
   - Update if mismatched (e.g., `.env`, config file)

### Gate 0: Environment Ready

- [ ] Backend port verified available or reassigned
- [ ] Backend health check confirms backend responds
- [ ] Frontend port (Metro bundler) verified available or reassigned
- [ ] Metro bundler status check confirms bundler responds
- [ ] Frontend API URL points to correct backend port

**Failure Action**: If backend cannot start on any port, EXIT with BLOCKED state. If Metro bundler cannot start on any port, EXIT with BLOCKED state.

---

## Exit States

| State | Criteria | Next Action |
|-------|----------|-------------|
| **SUCCESS** | All tests pass, report generated | Proceed with development/release |
| **PARTIAL** | Some tests fail, issues logged | Create tasks for HIGH/MEDIUM issues |
| **BLOCKED** | Build or deploy fails after retry | Escalate to orchestrator |

---

## Phase 1: Setup

**Objective**: Configure Xcode session and prepare simulator.

### Steps

1. **Verify environment**
   ```
   mcp__XcodeBuildMCP__doctor()
   ```

2. **Discover project files**
   ```
   mcp__XcodeBuildMCP__discover_projs({ workspaceRoot: "{project_root}" })
   ```

3. **List and select simulator**
   ```
   mcp__XcodeBuildMCP__list_sims()
   ```

4. **Set session defaults**
   ```
   mcp__XcodeBuildMCP__session-set-defaults({
     workspacePath: "{path/to/ios/App.xcworkspace}",
     scheme: "{AppName}",
     simulatorName: "{simulator_name}",
     useLatestOS: true,
     suppressWarnings: true
   })
   ```

5. **Reset simulator (if requested)**
   ```
   # Only if reset_simulator=true
   mcp__XcodeBuildMCP__erase_sims({ shutdownFirst: true })
   ```

6. **Boot simulator**
   ```
   mcp__XcodeBuildMCP__boot_sim()
   mcp__XcodeBuildMCP__open_sim()
   ```

### Gate 1: Setup Complete

- [ ] Workspace path resolved and valid
- [ ] Scheme identified
- [ ] Simulator selected and booted
- [ ] Session defaults configured

**Failure Action**: If environment check fails, EXIT with BLOCKED state.

---

## Phase 2: Build

**Objective**: Build the app for iOS simulator.

### Steps

1. **Install CocoaPods (React Native projects)**
   ```bash
   cd {project_root}/ios && pod install
   ```

2. **Build for simulator**
   ```
   mcp__XcodeBuildMCP__build_sim()
   ```

**Build timeout**: If build exceeds 15 minutes, run `mcp__XcodeBuildMCP__clean()` and retry once.

### Gate 2: Build Complete

- [ ] Pod dependencies installed (if applicable)
- [ ] Build completed without errors
- [ ] App bundle created

### Troubleshooting

| Issue | Resolution | Max Retries |
|-------|------------|-------------|
| Pod install fails | `pod repo update && pod install` | 1 |
| Signing error | Verify scheme uses "Debug" configuration | 0 (config fix) |
| Metro not found | `npx react-native start --reset-cache` | 1 |
| Derived data issues | `mcp__XcodeBuildMCP__clean()` then rebuild | 1 |

**Failure Action**: After troubleshooting retries exhausted, EXIT with BLOCKED state.

---

## Phase 3: Deploy

**Objective**: Install and launch the app on the simulator.

### Steps

1. **Get built app path**
   ```
   mcp__XcodeBuildMCP__get_sim_app_path({ platform: "iOS Simulator" })
   ```

2. **Install app**
   ```
   mcp__XcodeBuildMCP__install_app_sim({ appPath: "{app_path}" })
   ```

3. **Get bundle ID**
   ```
   mcp__XcodeBuildMCP__get_app_bundle_id({ appPath: "{app_path}" })
   ```

4. **Launch app**
   ```
   mcp__XcodeBuildMCP__launch_app_sim({ bundleId: "{bundle_id}" })
   ```

5. **Capture initial screenshot**
   ```
   mcp__XcodeBuildMCP__screenshot()
   ```
   Screenshot name: `01_initial_launch.png`

### Gate 3: Deploy Complete

- [ ] App installed on simulator
- [ ] App launched successfully
- [ ] Initial screen visible and captured

**Failure Action**: If launch fails, restart app via `stop_app_sim({ bundleId })` then `launch_app_sim({ bundleId })`. If still fails, EXIT with BLOCKED state.

---

## Phase 4: Test

**Objective**: Execute test flows with comprehensive visual/UX inspection, with Visual QA as a hard PASS/FAIL gate.

### Screenshot Visual Analysis (mandatory on every screenshot)

Every screenshot captured MUST be analyzed for the following before the test step is logged as PASS:

- [ ] **Layout not broken**: no elements overflowing their containers, no unexpected overlaps
- [ ] **No overflow issues**: content does not extend beyond the device screen boundaries; no clipped text or truncated UI regions
- [ ] **Responsiveness is intentional**: at each device size, elements reflow, stack, collapse into navigation drawers/sheets, and use native iOS patterns (bottom sheets, action sheets, compact navigation) — not just scaled down
- [ ] **Visual hierarchy coherent**: headings, content, and actions are legible and logically ordered on each device form factor

**If any of the above are violated**: the test step is marked **FAILED** (not a warning — a hard failure) with:
- Specific description of the issue
- Screenshot reference (filename)
- Device/simulator where it occurred

### Multi-Device Visual QA

Run all major screens on each of the following simulator devices. These map to the equivalent web viewport breakpoints.

| Device | Simulator | Equivalent Viewport | Expected Behavior |
|--------|-----------|---------------------|-------------------|
| iPhone SE (3rd gen) | iPhone SE | ~375px | Compact layout; all content reachable; touch targets ≥44pt |
| iPhone 16 Pro | iPhone 16 Pro | ~393px | Standard layout; primary design target |
| iPhone 16 Pro Max | iPhone 16 Pro Max | ~430px | Large display; no excessive whitespace |
| iPad (standard) | iPad (10th gen) | ~768px+ | Two-column or split view; navigation as sidebar |

**Each device must look intentionally designed for its form factor, not just stretched or squished.**

#### Use Case Validation Checklist (all devices)

- [ ] **Navigation on iPhone SE**: tab bar fully visible; touch targets ≥44pt height/width; no clipped labels
- [ ] **Forms usable on all devices**: text fields do not overflow; labels remain visible; keyboard does not permanently obscure the submit button (scroll or KeyboardAvoidingView required)
- [ ] **Data tables / lists responsive**: on compact devices, long rows use truncation with detail disclosure OR collapse to card layout — raw overflow that requires horizontal swipe without affordance is a FAIL
- [ ] **Modals and sheets**: bottom sheets and modals do not overflow the screen on any device; if content is long, the sheet itself must be scrollable

#### Screenshot Naming for Device Tests

```
{NN}_{flow}_{action}_{state}_{device}.png

Examples:
  20_dashboard_loaded_iphonese.png
  21_dashboard_loaded_iphone16pro.png
  22_dashboard_loaded_ipad.png
  23_profile_form_iphonese.png
  24_settings_sheet_iphone16promax.png
```

### Test Execution Pattern

For **every interaction**:

```
1. DESCRIBE  → mcp__XcodeBuildMCP__describe_ui()
2. SCREENSHOT (before) → mcp__XcodeBuildMCP__screenshot()
3. ACTION → tap / type / swipe / gesture
4. WAIT → Use postDelay parameter (default 400ms; 300ms for UI-only, 500ms for network)
5. SCREENSHOT (after) → mcp__XcodeBuildMCP__screenshot()
6. VERIFY → Check expected elements present
7. INSPECT → Run Visual Inspection Checklist
8. LOG → Record PASS/FAIL with notes
```

### Example: Testing Login Button

```
1. DESCRIBE: `mcp__XcodeBuildMCP__describe_ui()`
   → Find "Log In" button at (195, 450)
2. SCREENSHOT (before): `mcp__XcodeBuildMCP__screenshot()`
   → Save as 02_auth_login_ready.png
3. ACTION: `mcp__XcodeBuildMCP__tap({ label: "Log In" })`
4. WAIT: postDelay: 500ms (network call expected)
5. SCREENSHOT (after): `mcp__XcodeBuildMCP__screenshot()`
   → Save as 03_auth_login_loading.png
6. VERIFY: describe_ui shows loading indicator OR home screen elements
7. INSPECT: Run Visual Inspection Checklist - check loading state styling
8. LOG: "Login button tap: PASS - Loading indicator displayed, transitioned to home in 1.2s"
```

### Screenshot Naming Convention

```
{NN}_{flow}_{action}_{state}.png

Examples:
  01_auth_email_field_focused.png
  02_auth_password_entered.png
  03_auth_login_button_tapped.png
  04_auth_home_screen_loaded.png
  05_navigation_menu_opened.png
```

**CRITICAL**: Screenshots must be captured at EVERY state change. This is non-negotiable.

**Screenshot Storage**: Screenshots are stored in a temp directory during testing. Phase 5 copies them to the final report folder.

### Visual Inspection Checklist

At each screen/state, verify ALL of the following:

#### Layout & Styling
- [ ] **No broken layouts**: Elements don't overflow or overlap incorrectly
- [ ] **Consistent spacing**: Margins and padding look uniform
- [ ] **Proper alignment**: Text and elements align correctly
- [ ] **No cut-off text**: All text is fully visible
- [ ] **Correct colors**: Colors match expected theme/design
- [ ] **No weird fonts**: Font sizes readable (min 11pt), weights consistent across similar elements

#### Navigation & Flow
- [ ] **Clear navigation**: User can tell where they are
- [ ] **Back works**: Back button/gesture returns to expected screen
- [ ] **No dead ends**: User can always navigate away
- [ ] **Loading states**: Spinners/placeholders shown during loading
- [ ] **Transitions**: Animations run without stutter, dropped frames, or abrupt cuts

#### Interactive Elements
- [ ] **Buttons look tappable**: Visual affordance for interactive elements
- [ ] **Buttons respond**: Visual feedback on tap (color change, press state)
- [ ] **Links work**: All links navigate correctly
- [ ] **Forms validate**: Input validation shows clear feedback
- [ ] **Keyboards dismiss**: Keyboard closes when expected

#### UX Issues to Flag
- [ ] **Weird placement**: Elements in unexpected positions
- [ ] **Ugly interfaces**: Visual inconsistency (4+ unrelated colors, mixed corner radius styles, non-standard iOS components)
- [ ] **Confusing labels**: Unclear or misleading text
- [ ] **Missing feedback**: Actions happen with no visual confirmation
- [ ] **Broken images**: Missing or incorrectly sized images

### Edge Case Handling

| Situation | Detection | Action | Log Entry |
|-----------|-----------|--------|-----------|
| Screenshot fails | Tool returns error | Retry once, continue test | "SCREENSHOT_UNAVAILABLE at {step}" |
| describe_ui returns <5 elements | Minimal tree | Wait 1s, retry once, continue | "UI_TREE_MINIMAL at {step}" |
| Network timeout | >10s API wait | Mark test SKIPPED | "NETWORK_TIMEOUT: {endpoint}" |
| App crash | Process terminated | Restart app, retry test once | "APP_CRASH at {step}, retry: {result}" |
| Screenshot limit (15) | Counter reached | Save batch, prioritize remaining | "SCREENSHOT_LIMIT: {remaining} states" |
| Build timeout (15min) | Timer exceeded | Clean derived data, retry once | "BUILD_TIMEOUT, retrying after clean" |

### Test Scope Definitions

#### Smoke Test (`smoke`)
Critical path only - must complete in 5-10 minutes:
1. App launches successfully
2. Authentication flow (if applicable)
3. Primary feature/screen loads
4. Basic navigation works

#### Full Test (`full`)
All user flows - 15-30 minutes:
1. All smoke test items
2. All navigation paths
3. All form interactions
4. All CRUD operations
5. Edge cases (empty states, errors)
6. Settings/preferences
7. Logout/session management
8. **Multi-Device Visual QA** — mandatory across all four simulator device types:
   - **iPhone SE**: compact layout, touch targets ≥44pt, no clipped navigation labels
   - **iPhone 16 Pro**: standard layout, primary design target
   - **iPhone 16 Pro Max**: large display, no excessive whitespace gaps
   - **iPad**: split-view or two-column navigation, sidebar patterns
   At each device: screenshot every major screen and run the Screenshot Visual Analysis (see above). Any visual failure = test FAILED.

#### Specific Flow (`{flow_name}`)
Single flow as defined in app's test flow registry.

### MCP Tools Quick Reference

| Tool | Use For |
|------|---------|
| `describe_ui` | Get element tree with coordinates |
| `screenshot` | Capture current screen |
| `tap({ label: "X" })` | Tap by accessibility label |
| `tap({ x, y })` | Tap by coordinates |
| `type_text({ text: "X" })` | Enter text in focused field |
| `gesture({ preset: "scroll-down" })` | Scroll/swipe gestures |
| `key_press({ keyCode: 40 })` | Press Return/Enter |
| `swipe({ x1, y1, x2, y2 })` | Custom swipe |
| `long_press({ x, y, duration })` | Long press |

**Tap Target Selection**: Prefer `label` when accessibility label exists (more stable across UI changes). Use coordinates only when no label is available or label is ambiguous.

**Common Key Codes**:
| Key | Code |
|-----|------|
| Return/Enter | 40 |
| Backspace | 42 |
| Tab | 43 |
| Space | 44 |

### Gate 4: Testing Complete

- [ ] All flows in scope executed
- [ ] Screenshot captured for EVERY state change
- [ ] Visual Inspection Checklist applied to every unique screen (not every screenshot of same screen)
- [ ] All issues documented with severity and reproduction steps

> **Note**: See [Constraints](#constraints) section for screenshot limits and other restrictions.

---

## Phase 5: Report

**Objective**: Generate comprehensive test report with findings.

### Step 1: Create Report Directory

```bash
mkdir -p knowledge/qa/test-reports/{YYYY-MM-DD}T{HH-MM}_{test-name}/screenshots
```

### Step 2: Document Screenshots

Move/copy all captured screenshots to the screenshots folder with proper naming.

### Step 3: Generate Markdown Report

Create `report.md` using the template below.

### Step 4: Generate Google Doc (if enabled)

If `create_google_doc=true`:

1. Read the completed markdown report
2. Use Google Docs MCP tools:
   ```
   mcp__google-workspace__docs_create({
     title: "{App Name} - Test Report - {date}",
     markdown: "{report_content}"
   })
   ```
3. Add Google Docs URL to markdown report

### Step 5: Create Tasks for Issues

For each HIGH or MEDIUM priority issue:
```
TaskCreate({
  subject: "Fix: {issue description}",
  description: "From test report {path}:\n\n{issue details}\n\nReproduction: {steps}",
  activeForm: "Fixing {issue}"
})
```

### Step 6: Update Project Status

Add summary to `knowledge/project-status.md`:
```markdown
## Recent Test Results - {date}
- **Pass Rate**: {X}%
- **Issues Found**: {count} (HIGH: {n}, MEDIUM: {n}, LOW: {n})
- **Report**: `knowledge/qa/test-reports/{folder}/report.md`
```

### Gate 5: Report Complete

- [ ] Report directory created
- [ ] All screenshots saved with correct naming
- [ ] Markdown report generated
- [ ] Google Doc created (if enabled)
- [ ] Tasks created for HIGH/MEDIUM issues
- [ ] Project status updated

---

## Report Template

Use the template at [`templates/ios-test-report-template.md`](../templates/ios-test-report-template.md).

Key sections to complete:
- Executive Summary with pass rate
- Test Results by Flow with status table
- Issues Found organized by severity
- **Visual QA** — hard gate section (see below); must appear before the UX Assessment
- Visual/UX Assessment
- Recommendations

### Visual QA Section (required in every report)

> This section is a hard gate. Any FAIL here means the overall test result is FAILED regardless of functional test outcomes.

Include a **Visual QA** section in the report with the following structure:

#### Device Pass/Fail Summary

| Device | Simulator | Status | Issues Found |
|--------|-----------|--------|--------------|
| iPhone SE | iPhone SE (3rd gen) | PASS / FAIL | {count} |
| iPhone 16 Pro | iPhone 16 Pro | PASS / FAIL | {count} |
| iPhone 16 Pro Max | iPhone 16 Pro Max | PASS / FAIL | {count} |
| iPad | iPad (10th gen) | PASS / FAIL | {count} |

#### Issues by Device

For each device with issues:

| # | Issue Description | Screenshot | Severity |
|---|-------------------|------------|----------|
| 1 | {specific description} | {filename} | FAIL |

#### Overall Visual QA Verdict

**PASS** — No visual issues found across all simulator devices.

_or_

**FAIL** — Visual issues found. All issues must be resolved before this pipeline reports SUCCESS.

> **Follow-up task**: Update `templates/ios-test-report-template.md` to incorporate the Visual QA section structure above as a permanent template section.

---

## Troubleshooting Reference

| Problem | Solution |
|---------|----------|
| Simulator won't boot | `xcrun simctl shutdown all` then retry |
| App crashes on launch | Check Metro, try `--reset-cache` |
| UI elements not found | Use `describe_ui` to see actual labels |
| Tap not registering | Verify coordinates, add `postDelay` |
| Build takes too long | Enable `suppressWarnings: true` |
| Cannot find workspace | Verify path with `discover_projs` |

---

## Constraints

- Maximum ~15 screenshots per session (MCP limitation)
- Screenshots are ephemeral unless manually saved
- Test duration limited by session timeout
- Cannot test physical device features (Face ID, etc.)
- Network tests require backend services running

---

## Related Documentation

- Skill implementation: `.claude/skills/test-ios/SKILL.md`
- Workflows: `../workflows/README.md`
- QA Tester agent: `.claude/agents/qa-tester.md`

---

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 2.4 | 2026-02-23 | Added Screenshot Visual Analysis (mandatory on every screenshot); added Multi-Device Visual QA subsection in Phase 4 mapping viewport breakpoints to iOS simulator devices (iPhone SE ≈ 375px, iPhone 16 Pro ≈ 393px, iPad ≈ 768px+); added Use Case Validation Checklist (mobile nav, forms, tables, modals/sheets); added Visual QA report section structure as hard PASS/FAIL gate before UX Assessment; expanded Phase 4 Full Test to include multi-device Visual QA step |
| 2.3 | 2026-01-28 | Added frontend port (Metro bundler) validation to Phase 0; expanded Gate 0 checklist |
| 2.2 | 2026-01-27 | Applied critical fixes: Key Definitions section, 8-step example, clarified restart scope |
| 2.1 | 2026-01-27 | Added Phase 0: Port & Connectivity Validation for multi-project environments |
| 2.0 | 2026-01-27 | Restructured as pipeline; added Visual Inspection Checklist; enhanced screenshot protocol |
| 1.0 | 2026-01-27 | Initial skill version |
