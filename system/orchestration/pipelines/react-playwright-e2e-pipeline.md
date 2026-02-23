# React Playwright E2E Pipeline

<!-- Last Updated: 2026-02-23 -->
<!-- Status: Current -->
<!-- Author: pristo -->

> Owner: QA Tester / Frontend Developer | Version: 1.1

A linear, staged pipeline for comprehensive E2E testing of React web applications using Playwright. Takes screenshots of all user flows, evaluates visual/UX quality, and generates a PDF report with improvement suggestions.

---

## Pipeline Overview

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                         React Playwright E2E Pipeline                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│                                                                                    │
│ ┌───────┐   ┌───────┐   ┌───────┐   ┌───────┐   ┌───────┐   ┌───────┐            │
│ │ SETUP │──►│VERIFY │──►│ FLOWS │──►│ TEST  │──►│ASSESS │──►│REPORT │            │
│ └───────┘   └───────┘   └───────┘   └───────┘   └───────┘   └───────┘            │
│     │           │           │           │           │           │                 │
│     ▼           ▼           ▼           ▼           ▼           ▼                 │
│ [Gate 0]    [Gate 1]    [Gate 2]    [Gate 3]    [Gate 4]    [Gate 5]             │
│ Env Ready   App Live    Flows OK   Tests Done  UX Scored   PDF Ready            │
│                                                                                    │
└───────────────────────────────────────────────────────────────────────────────────┘

Exit States:
  ✓ SUCCESS  - All tests pass, PDF report generated
  ◐ PARTIAL  - Some tests fail, issues documented in report
  ✗ BLOCKED  - Cannot reach app or Playwright fails, escalate
```

---

## Table of Contents

- [Parameters](#parameters)
- [Key Definitions](#key-definitions)
- [Preconditions](#preconditions)
- [Phase 0: Environment Setup](#phase-0-environment-setup)
- [Phase 1: App Verification](#phase-1-app-verification)
- [Phase 2: Flow Discovery](#phase-2-flow-discovery)
- [Phase 3: Test Execution](#phase-3-test-execution)
- [Phase 4: Visual & UX Assessment](#phase-4-visual--ux-assessment)
- [Phase 5: Report Generation](#phase-5-report-generation)
- [Troubleshooting Reference](#troubleshooting-reference)
- [Constraints](#constraints)

---

## Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `app_url` | Yes | — | Base URL of the running app (e.g., `http://localhost:18790`) |
| `test_scope` | No | `full` | Test scope: `full`, `smoke`, or specific flow name |
| `browser` | No | `chromium` | Browser engine: `chromium`, `firefox`, `webkit` |
| `viewport` | No | `1280x720` | Default browser viewport size |
| `viewports` | No | `1920,1280,768,375` | Comma-separated list of widths for multi-viewport Visual QA |
| `dark_mode` | No | `true` | Test in dark mode (prefers-color-scheme: dark) |
| `generate_pdf` | No | `true` | Generate PDF report |
| `screenshot_dir` | No | auto | Directory for screenshots |

---

## Key Definitions

| Term | Definition |
|------|------------|
| **State change** | Any visible UI modification: page navigation, modal open/close, form field change, loading indicator, error/success message, list update, tab selection |
| **User flow** | A sequence of actions achieving a goal (e.g., create a project, view dashboard, edit a contact) |
| **Critical path** | Minimum user journey for app to deliver primary value (dashboard → entity list → create → edit → delete) |
| **Visual regression** | Unintended visual change from expected appearance |

---

## Preconditions

- [ ] App is running and accessible at `app_url`
- [ ] Playwright is installed (`npx playwright --version`)
- [ ] Chromium browser available (`npx playwright install chromium` if needed)
- [ ] Node.js available for Playwright scripts

---

## Phase 0: Environment Setup

**Objective**: Prepare Playwright environment and verify tooling.

### Steps

1. **Check Playwright installation**
   ```bash
   npx playwright --version || npm install -D playwright @playwright/test
   ```

2. **Ensure browser is available**
   ```bash
   npx playwright install chromium
   ```

3. **Create test directory**
   ```bash
   mkdir -p e2e-tests/screenshots
   mkdir -p e2e-tests/reports
   ```

4. **Create Playwright config** (if not exists)
   ```typescript
   // e2e-tests/playwright.config.ts
   import { defineConfig, devices } from '@playwright/test';

   // Multi-viewport projects: each entry runs the full test suite at one viewport.
   // Visual QA failures at any viewport mark the entire run as FAILED.
   export default defineConfig({
     testDir: './tests',
     timeout: 30000,
     use: {
       baseURL: '{app_url}',
       screenshot: 'on',
       trace: 'on-first-retry',
       colorScheme: 'dark',
     },
     reporter: [['html', { open: 'never' }]],
     projects: [
       {
         name: 'desktop-1920',
         use: { viewport: { width: 1920, height: 1080 } },
       },
       {
         name: 'laptop-1280',
         use: { viewport: { width: 1280, height: 720 } },
       },
       {
         name: 'tablet-768',
         use: { viewport: { width: 768, height: 1024 } },
       },
       {
         name: 'mobile-375',
         use: { viewport: { width: 375, height: 812 } },
       },
     ],
   });
   ```

### Gate 0: Environment Ready

- [ ] Playwright installed and working
- [ ] Browser available
- [ ] Test directories created
- [ ] Config file ready

**Failure Action**: If Playwright cannot install, EXIT with BLOCKED state.

---

## Phase 1: App Verification

**Objective**: Confirm the app is running and accessible.

### Steps

1. **Health check**
   ```bash
   curl -s -o /dev/null -w "%{http_code}" {app_url}
   ```
   Expected: 200

2. **API health check** (if applicable)
   ```bash
   curl -s {app_url}/api/dashboard || curl -s {app_url}/api/health
   ```

3. **Take initial screenshot**
   ```typescript
   const page = await browser.newPage();
   await page.goto('{app_url}');
   await page.screenshot({ path: 'screenshots/00_initial_load.png', fullPage: true });
   ```

4. **Verify page loads with content** (not blank/error)
   ```typescript
   await expect(page.locator('body')).not.toBeEmpty();
   ```

### Gate 1: App Live

- [ ] HTTP 200 from app URL
- [ ] Page renders with visible content
- [ ] Initial screenshot captured
- [ ] No console errors on load (or only expected warnings)

**Failure Action**: If app unreachable after 3 retries (5s apart), EXIT with BLOCKED state.

---

## Phase 2: Flow Discovery

**Objective**: Identify all testable user flows from the UI.

### Steps

1. **Discover navigation structure**
   ```typescript
   // Find all navigation links
   const navLinks = await page.locator('nav a, aside a, [role="navigation"] a').all();
   for (const link of navLinks) {
     const text = await link.textContent();
     const href = await link.getAttribute('href');
     console.log(`Nav: ${text} → ${href}`);
   }
   ```

2. **Map entity pages** — for each navigation item:
   - Navigate to the page
   - Screenshot the list view
   - Identify CRUD actions (create button, edit links, delete buttons)
   - Identify filters/search

3. **Define test flows** based on discovery:

   **Smoke flows** (always test):
   - Dashboard loads with summary data
   - Navigate to each entity list
   - Verify data displays

   **Full flows** (test all):
   - Dashboard: all cards render, data is accurate
   - Per entity: list → create → verify in list → view detail → edit → verify change → delete → verify removal
   - Navigation: sidebar links work, active state highlights
   - Search/filter: if present, verify functionality
   - Responsive: resize viewport, check layout doesn't break
   - Error states: invalid form submission, API error handling

4. **Document flows** in test plan before executing

### Gate 2: Flows Identified

- [ ] All navigation items discovered
- [ ] CRUD operations identified per entity
- [ ] Test flows documented
- [ ] Screenshot of each page captured during discovery

**Failure Action**: If fewer than 2 navigable pages found, EXIT with PARTIAL (app may be incomplete).

---

## Phase 3: Test Execution

**Objective**: Execute all test flows with comprehensive screenshots.

### Test Execution Pattern

For **every interaction**:

```
1. SCREENSHOT (before) → capture current state
2. ACTION → click / type / navigate / submit
3. WAIT → wait for network idle or specific element
4. SCREENSHOT (after) → capture resulting state
5. VERIFY → check expected elements present
6. INSPECT → run Visual Inspection Checklist
7. LOG → record PASS/FAIL with notes
```

### Screenshot Naming Convention

```
{NN}_{flow}_{action}_{state}.png

Examples:
  01_dashboard_initial_loaded.png
  02_projects_list_loaded.png
  03_projects_create_form_open.png
  04_projects_create_form_filled.png
  05_projects_create_submitted.png
  06_projects_list_new_item_visible.png
  07_projects_detail_view.png
  08_projects_edit_form_open.png
  09_projects_edit_submitted.png
  10_projects_delete_confirmation.png
  11_projects_delete_completed.png
```

**CRITICAL**: Screenshots must be captured at EVERY state change. This is non-negotiable.

### Playwright Test Template

```typescript
import { test, expect, Page } from '@playwright/test';

test.describe('Entity CRUD Flow', () => {
  let screenshotCounter = 0;

  async function screenshot(page: Page, name: string) {
    screenshotCounter++;
    const filename = `${String(screenshotCounter).padStart(2, '0')}_${name}.png`;
    await page.screenshot({ path: `screenshots/${filename}`, fullPage: true });
    return filename;
  }

  test('dashboard loads', async ({ page }) => {
    await page.goto('/');
    await page.waitForLoadState('networkidle');
    await screenshot(page, 'dashboard_loaded');
    // Verify dashboard elements
    await expect(page.locator('[data-testid="dashboard"]')).toBeVisible();
  });

  test('entity CRUD', async ({ page }) => {
    // List
    await page.goto('/entities');
    await page.waitForLoadState('networkidle');
    await screenshot(page, 'entity_list');

    // Create
    await page.click('button:has-text("Create"), a:has-text("Create"), button:has-text("New"), a:has-text("Add")');
    await screenshot(page, 'entity_create_form');
    // Fill form...
    await screenshot(page, 'entity_create_filled');
    // Submit...
    await screenshot(page, 'entity_create_submitted');

    // Verify in list
    await screenshot(page, 'entity_list_with_new');

    // Edit
    // ... click edit, fill, submit, screenshot each step

    // Delete
    // ... click delete, confirm, screenshot each step
  });
});
```

### Edge Case Handling

| Situation | Detection | Action | Log Entry |
|-----------|-----------|--------|-----------|
| Page takes >10s to load | Timeout | Retry once, continue | "SLOW_LOAD at {step}" |
| Element not found | Playwright timeout | Screenshot current state, skip step | "ELEMENT_NOT_FOUND: {selector}" |
| API error on page | Error message visible | Screenshot, log, continue | "API_ERROR at {step}" |
| Empty list (no data) | No list items | Screenshot empty state, log | "EMPTY_STATE at {step}" |
| Form validation error | Error elements visible | Screenshot, document | "VALIDATION: {details}" |
| App crash / blank page | No content | Retry navigation once | "APP_ERROR at {step}" |

### Test Scope Definitions

#### Smoke Test (`smoke`)
Critical path only — 5-10 minutes:
1. App loads, dashboard displays
2. Navigate to each entity list
3. Verify data renders
4. One create + delete cycle

#### Full Test (`full`)
All user flows — 15-30 minutes:
1. All smoke items
2. Full CRUD for every entity
3. Form validation (invalid inputs)
4. Search/filter functionality
5. Navigation (all sidebar links)
6. **Multi-Viewport Visual QA** — mandatory at all four breakpoints:
   - **1920px** (desktop): full layout, all columns visible, no wasted whitespace
   - **1280px** (laptop): standard layout, primary target
   - **768px** (tablet): two-column collapse, navigation reflows, no horizontal scroll
   - **375px** (mobile): single-column, hamburger menus active, touch targets ≥44px CSS
   At each breakpoint: screenshot every major screen and run the Screenshot Visual Analysis (see Phase 4). Any visual failure = test FAILED.
7. Error states
8. Empty states

---

## Phase 4: Visual & UX Assessment

**Objective**: Evaluate visual quality and UX of every unique screen, with Visual QA as a hard PASS/FAIL gate.

### Screenshot Visual Analysis (mandatory on every screenshot)

Every screenshot captured MUST be analyzed for the following before the test step is logged as PASS:

- [ ] **Layout not broken**: no elements overflowing their containers, no unexpected overlaps
- [ ] **No overflow issues**: tables, cards, and text do not extend beyond their containers; horizontal scrollbars do not appear at expected viewports
- [ ] **Responsiveness is intentional**: elements reflow, stack, collapse into menus, and adopt mobile patterns — not just shrunk. The layout must look intentionally designed at each breakpoint.
- [ ] **Visual hierarchy coherent**: headings, content, and actions are legible and logically ordered at each breakpoint

**If any of the above are violated**: the test step is marked **FAILED** (not a warning — a hard failure) with:
- Specific description of the issue
- Screenshot reference (filename)
- Viewport width where it occurred

### Multi-Viewport Visual QA

Run all major screens at each of the following viewports. Use Playwright `projects` config (see Phase 0) to execute tests at all widths automatically.

| Viewport | Width | Expected Behavior |
|----------|-------|-------------------|
| Desktop | 1920px | Full layout; all columns/panels visible; no wasted whitespace |
| Laptop | 1280px | Standard layout; primary design target |
| Tablet | 768px | Two-column collapse; navigation reflows; no horizontal scroll |
| Mobile | 375px | Single-column; hamburger menu active; touch targets ≥44px CSS |

**Each viewport must look intentionally designed, not just squished.**

#### Use Case Validation Checklist (all viewports)

- [ ] **Navigation on mobile (375px)**: hamburger menu opens/closes correctly; touch targets ≥44px CSS height/width
- [ ] **Forms usable at all widths**: inputs do not overflow; labels remain visible; submit button is reachable without scrolling past the fold (or scrollable with clear affordance)
- [ ] **Data tables responsive**: on 768px and below, tables use horizontal scroll with sticky first column OR collapse to card layout — raw horizontal overflow without scroll is a FAIL
- [ ] **Modals and sheets**: do not overflow the viewport on any breakpoint; if content is long, the modal itself is scrollable (not the page behind it)

#### Screenshot Naming for Viewport Tests

```
{NN}_{flow}_{action}_{state}_{viewport}px.png

Examples:
  20_dashboard_loaded_1920px.png
  21_dashboard_loaded_1280px.png
  22_dashboard_loaded_768px.png
  23_dashboard_loaded_375px.png
  24_projects_list_loaded_375px.png
```

### Visual Inspection Checklist

At each unique screen, verify ALL of the following:

#### Layout & Styling
- [ ] **No broken layouts**: elements don't overflow or overlap
- [ ] **Consistent spacing**: margins and padding look uniform
- [ ] **Proper alignment**: text and elements align correctly
- [ ] **No cut-off text**: all text fully visible
- [ ] **Dark theme consistent**: no jarring light elements in dark UI
- [ ] **Font consistency**: sizes readable (min 14px body), weights consistent

#### Navigation & Flow
- [ ] **Clear navigation**: user can tell where they are (active sidebar item)
- [ ] **Breadcrumbs or back**: user can return to previous page
- [ ] **No dead ends**: user can always navigate away
- [ ] **Loading indicators**: shown during data fetch
- [ ] **Page transitions**: no blank flashes between routes

#### Interactive Elements
- [ ] **Buttons look clickable**: visual affordance (color, hover state)
- [ ] **Hover states**: interactive elements respond to hover
- [ ] **Focus indicators**: keyboard focus is visible
- [ ] **Forms validate**: input validation shows clear inline feedback
- [ ] **Disabled states**: buttons disabled during submission

#### Data Display
- [ ] **Tables readable**: columns properly sized, text not truncated
- [ ] **Empty states**: meaningful message when no data
- [ ] **Pagination/scrolling**: large lists handle overflow
- [ ] **Numbers formatted**: dates, counts, percentages displayed properly
- [ ] **Status indicators**: color-coded badges/chips for status fields

#### UX Issues to Flag
- [ ] **Confusing labels**: unclear or misleading text
- [ ] **Missing feedback**: actions happen with no visual confirmation
- [ ] **Inconsistent patterns**: same action looks different in different places
- [ ] **Too many clicks**: common actions require excessive navigation
- [ ] **Information overload**: too much data without hierarchy

### Scoring

Rate each screen 1-10 on:
1. **Visual Quality** (layout, styling, consistency)
2. **Usability** (navigation, discoverability, feedback)
3. **Completeness** (all states handled: loading, error, empty, success)

### UI/UX Improvement Suggestions

For each screen, provide:
- What works well
- What could be improved (with specific suggestions)
- Priority: HIGH (usability blocker), MEDIUM (noticeable issue), LOW (polish)

---

## Phase 5: Report Generation

**Objective**: Generate a comprehensive PDF report.

### Step 1: Create Report Directory

```bash
mkdir -p knowledge/qa/test-reports/{YYYY-MM-DD}T{HH-MM}_{app-name}/screenshots
```

### Step 2: Collect Screenshots

Copy all captured screenshots to the report directory with proper naming.

### Step 3: Generate Markdown Report

Create `report.md` using the template below.

### Step 4: Generate PDF

Convert markdown + screenshots to PDF:

```bash
# Option A: Using md-to-pdf (npm)
npx md-to-pdf report.md --stylesheet styles.css

# Option B: Using Playwright itself
const page = await browser.newPage();
await page.setContent(htmlReport);
await page.pdf({ path: 'report.pdf', format: 'A4', printBackground: true });
```

Preferred: **Option B** (Playwright PDF) — generates from HTML template with embedded screenshots, better layout control.

### Step 5: Create Tasks for Issues

For each HIGH or MEDIUM priority issue, document in:
- `knowledge/qa/test-reports/{folder}/issues.md`

### Step 6: Update Project Status

Add summary to `knowledge/project-status.md`.

### Gate 5: Report Complete

- [ ] All screenshots saved
- [ ] Markdown report generated
- [ ] PDF report generated
- [ ] Issues documented
- [ ] Project status updated

---

## Report Template

```markdown
# E2E Test Report: {App Name}

**Date**: {YYYY-MM-DD HH:MM}
**App URL**: {url}
**Browser**: {browser} {version}
**Viewport**: {width}x{height}
**Test Scope**: {full/smoke}
**Duration**: {minutes}

---

## Executive Summary

- **Total Flows Tested**: {count}
- **Pass Rate**: {percentage}%
- **Screenshots Captured**: {count}
- **Issues Found**: {total} (HIGH: {n}, MEDIUM: {n}, LOW: {n})
- **Overall UX Score**: {average}/10

---

## Dashboard

![Dashboard](screenshots/01_dashboard_loaded.png)

**UX Score**: {n}/10

**What works**: {observations}
**Improvements**: {suggestions}

---

## Test Results by Flow

### {Entity Name} CRUD

| Step | Action | Expected | Actual | Status | Screenshot |
|------|--------|----------|--------|--------|------------|
| 1 | Navigate to list | List page loads | {result} | PASS/FAIL | {filename} |
| 2 | Click Create | Form opens | {result} | PASS/FAIL | {filename} |
| 3 | Fill form | Fields accept input | {result} | PASS/FAIL | {filename} |
| 4 | Submit | New item in list | {result} | PASS/FAIL | {filename} |
| 5 | View detail | Detail page loads | {result} | PASS/FAIL | {filename} |
| 6 | Edit | Edit form opens | {result} | PASS/FAIL | {filename} |
| 7 | Save edit | Changes reflected | {result} | PASS/FAIL | {filename} |
| 8 | Delete | Confirmation shown | {result} | PASS/FAIL | {filename} |
| 9 | Confirm delete | Item removed | {result} | PASS/FAIL | {filename} |

[Repeat for each entity]

---

## Visual QA

> This section is a hard gate. Any FAIL here means the overall test result is FAILED regardless of functional test outcomes.

### Viewport Pass/Fail Summary

| Viewport | Width | Status | Issues Found |
|----------|-------|--------|--------------|
| Desktop | 1920px | PASS / FAIL | {count} |
| Laptop | 1280px | PASS / FAIL | {count} |
| Tablet | 768px | PASS / FAIL | {count} |
| Mobile | 375px | PASS / FAIL | {count} |

### Issues by Viewport

#### 1920px — Desktop
| # | Issue Description | Screenshot | Severity |
|---|-------------------|------------|----------|
| 1 | {specific description} | {filename} | FAIL |

#### 1280px — Laptop
| # | Issue Description | Screenshot | Severity |
|---|-------------------|------------|----------|
| 1 | {specific description} | {filename} | FAIL |

#### 768px — Tablet
| # | Issue Description | Screenshot | Severity |
|---|-------------------|------------|----------|
| 1 | {specific description} | {filename} | FAIL |

#### 375px — Mobile
| # | Issue Description | Screenshot | Severity |
|---|-------------------|------------|----------|
| 1 | {specific description} | {filename} | FAIL |

### Overall Visual QA Verdict

**PASS** — No visual issues found across all viewports.

_or_

**FAIL** — Visual issues found. See issues listed above. All issues must be resolved before this pipeline reports SUCCESS.

---

## Visual & UX Assessment

### Screen Scores

| Screen | Visual Quality | Usability | Completeness | Average |
|--------|---------------|-----------|--------------|---------|
| Dashboard | {n}/10 | {n}/10 | {n}/10 | {n}/10 |
| {Entity} List | {n}/10 | {n}/10 | {n}/10 | {n}/10 |
| {Entity} Form | {n}/10 | {n}/10 | {n}/10 | {n}/10 |
| ... | ... | ... | ... | ... |

### UI/UX Improvement Suggestions

#### HIGH Priority
1. {suggestion with specific screen + fix}

#### MEDIUM Priority
1. {suggestion with specific screen + fix}

#### LOW Priority
1. {suggestion — polish items}

---

## Issues Found

| # | Severity | Screen | Description | Screenshot |
|---|----------|--------|-------------|------------|
| 1 | HIGH | {screen} | {description} | {filename} |
| 2 | MEDIUM | {screen} | {description} | {filename} |

---

## Accessibility Notes

- Keyboard navigation: {assessment}
- Color contrast: {assessment}
- Screen reader compatibility: {assessment}
- Focus management: {assessment}

---

## Recommendations

1. {actionable recommendation}
2. {actionable recommendation}
```

---

## Troubleshooting Reference

| Problem | Solution |
|---------|----------|
| Playwright install fails | `npx playwright install --with-deps chromium` |
| App not reachable | Check Docker is running, port is correct |
| Screenshots blank | Increase wait time before screenshot |
| PDF generation fails | Fallback to markdown report |
| Timeout on navigation | Increase timeout in config, check if app is slow |
| Element selectors break | Use role-based selectors (`getByRole`, `getByText`) |

---

## Constraints

- Screenshots are stored locally (not uploaded anywhere)
- PDF generation requires HTML template (Playwright PDF from page)
- Test duration limited by Playwright timeout settings
- Cannot test server-side rendering (app must be client-rendered or SSR-hydrated)
- Responsive testing limited to viewport resize (not real device testing)

---

## Related Documentation

- iOS Testing Pipeline: `../pipelines/ios-testing-pipeline.md`
- Standard Feature Flow: `../workflows/standard-feature-flow.md`
- QA Tester agent: `.claude/agents/qa-tester.md`
- React Vite Code Judge: `.claude/agents/react-vite-code-judge.md`

---

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 1.1 | 2026-02-23 | Added Screenshot Visual Analysis (mandatory on every screenshot); added Multi-Viewport Visual QA subsection in Phase 4 with explicit 1920/1280/768/375px breakpoints; added Use Case Validation Checklist (mobile nav, forms, tables, modals); added Visual QA section to Report Template as hard PASS/FAIL gate before UX Assessment; updated Playwright config example to show multi-viewport projects; expanded Phase 3 Full Test responsive checks |
| 1.0 | 2026-02-21 | Initial version. Adapted from iOS Testing Pipeline for React web + Playwright |
