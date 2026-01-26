# Feedback Pipeline

> Owner: Documentation Agent | Version: 1.1 | Last Updated: 2026-01-26

Execute a structured 3-phase feedback pipeline to analyze: **$ARGUMENTS**

This pipeline produces prioritized, actionable recommendations through parallel multi-perspective analysis. It does NOT modify the target files -- it only reads and analyzes them.

## Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| target | Yes | File, folder, or system to analyze (passed as $ARGUMENTS) |

## Preconditions

- [ ] Target files must exist and be readable
- [ ] User has approved running the pipeline
- [ ] For system-level targets (CLAUDE.md, .claude/, prompt-templates/), snapshot should be taken before implementing results

## Exit States

- **Success**: `final_feedback.md` created with validated findings in `feedbacks/<timestamp>_<topic-slug>/`
- **Failure**: Pipeline halted due to missing target, user cancellation, or unrecoverable subagent failures
- **Partial**: Some subagents failed; report generated with available data (failures noted in report)

---

## Pre-Pipeline Check

Before starting the pipeline, scan `feedbacks/feedback-ideas/` for existing quick ideas related to the target:

1. Read `feedbacks/feedback-ideas/INDEX.md` for a quick overview
2. Check if any captured ideas overlap with the analysis target
3. If related ideas exist:
   - Note them in `pipeline_input.md` under a "Related Quick Ideas" section
   - Consider whether they should be consolidated with pipeline findings
   - Flag potential duplicates in the final report

This prevents re-discovering insights that were already captured as quick ideas.

---

## Setup

1. Determine the **scope**: Based on "$ARGUMENTS", identify the specific files, folders, or systems to analyze. This scope must be passed to EVERY subagent so they know exactly what to read.
   - **Recommendation Scope:** Recommendations MUST only modify primary targets. If analysis reveals issues in context files (files read for understanding but not specified as targets), flag those as "Out of Scope" in the final report rather than including them as actionable items.
   - **System-Level Targets:** If the target includes `CLAUDE.md`, `.claude/`, or `prompt-templates/`, flag this as a system-level analysis. The final report should recommend caution to avoid cluttering these files with too many lines, and suggest taking a snapshot before implementing changes.
2. Derive a **timestamped folder name** using the current time in `YYYY-MM-DDThh-mm-ss` format followed by a topic slug:
   - Generate timestamp from current time (e.g., `2026-01-25T14-30-00`)
   - Strip leading articles and possessives from the topic (the, a, an, our, my, their)
   - Convert remaining words to lowercase kebab-case
   - Keep the topic slug short but descriptive (3-6 words max)
   - Combine as: `{timestamp}_{topic-slug}`
   - Examples: "the CLAUDE.md and agent templates" -> `2026-01-25T14-30-00_claude-md-and-agent-templates`, "our backend API design" -> `2026-01-25T14-30-00_backend-api-design`
3. Create a `feedbacks/<timestamp>_<topic-slug>/` directory at `{project-root}/feedbacks/...` where project-root is the directory containing `CLAUDE.md`.
4. Create a `pipeline_input.md` file to record the original request:
   ```
   feedbacks/<timestamp>_<topic-slug>/pipeline_input.md
   ```

   **Format:**
   ```markdown
   # Pipeline Input Record

   **Initiated By**: [user / orchestrator / retrospective-agent / other]
   **Timestamp**: [ISO 8601 timestamp when pipeline started]
   **Session**: [session ID or context if available]

   ## Original Request

   > [paste the exact $ARGUMENTS as provided, verbatim, preserving formatting]

   ## Interpreted Scope

   - **Target files/systems**: [list of files/folders to analyze]
   - **Topic slug**: [the derived slug]
   - **Analysis focus**: [brief description of what we're trying to improve]
   ```

   This file serves as an audit trail and ensures the original intent is preserved even if the analysis diverges.

---

## Ideation Phase

**Goal:** Generate diverse feedback questions from 5 distinct analytical perspectives.

Launch **5 subagents IN PARALLEL**, each assigned one of the following angles:

| # | Angle | Focus |
|---|-------|-------|
| 1 | Structure & Design | Organization, modularity, separation of concerns, naming conventions, file layout, information architecture |
| 2 | Content & Quality | Accuracy, completeness, clarity, consistency, appropriate level of detail, missing information |
| 3 | Potential Errors & Risks | Contradictions, ambiguities, edge cases, failure modes, security concerns, incorrect assumptions |
| 4 | Strengths & Patterns | What works well, best practices already in use, patterns worth preserving, effective conventions |
| 5 | Innovation & Improvements | Missing capabilities, modernization opportunities, efficiency gains, alternative approaches, scalability |

**Important:** Angle #4 (Strengths & Patterns) follows a SEPARATE track through the pipeline. Do not merge strength-related questions with problem-focused groups in the Analysis Phase.

**Each subagent prompt must include:**
- The exact scope of files/systems to analyze (from "$ARGUMENTS")
- Their assigned angle and focus area
- Instruction to brainstorm **8-12 specific feedback questions** about the target
- Questions should be concrete and investigative, not generic (e.g., "Does the error handling in X cover timeout scenarios?" rather than "Is error handling good?")
- Instruction to READ the actual target files before generating questions
- Reminder: Do NOT modify any target files

**Output:** Collect all feedback questions from the 5 subagents. You should have 40-60 total questions covering diverse angles.

---

## Analysis Phase

**Goal:** Group feedback questions thematically, then provide detailed analytical responses.

### Grouping

Review ALL questions from the Ideation Phase and organize them into **5 thematic groups**. Groups should be based on the THEMES that emerged (which may differ from the Ideation Phase angles -- e.g., multiple angles might raise questions about "error handling" which becomes its own group).

**Exception for Strengths:** Questions from Angle #4 (Strengths & Patterns) should remain in their own dedicated group called "Strengths & Patterns to Preserve". Do NOT scatter these across other thematic groups. This ensures strengths survive the analysis pipeline intact.

You will create:
- 4 thematic groups for problem/improvement questions (from angles 1, 2, 3, 5)
- 1 dedicated strengths group (from angle 4)

For each group, create a request file:
```
feedbacks/<timestamp>_<topic-slug>/feedback_request_group_<N>_<short-kebab-title>.md
```

Each file should contain:
- Group title and theme description
- The collected questions belonging to that theme
- The scope of files to analyze

### Analysis

Launch **5 subagents IN PARALLEL**, one per request file.

**Each subagent prompt must include:**
- Instruction to read their assigned `feedbacks/<timestamp>_<topic-slug>/feedback_request_group_<N>_*.md` file
- Instruction to READ the ACTUAL current state of ALL target files relevant to the questions (do not assume or hallucinate file contents)
- For each question, provide:
  - A detailed analytical response citing specific lines, sections, or patterns from actual files
  - A severity rating: `Critical` / `Major` / `Minor` / `Observation`
  - A concrete, actionable recommendation (what specifically to change and how)
- Instruction to save output to: `feedbacks/<timestamp>_<topic-slug>/feedback_response_group_<N>_<short-kebab-title>.md`
- Reminder: Do NOT modify any target files

**Severity definitions:**
- **Critical**: Blocks functionality, causes failures, or creates significant risk. Must fix immediately.
- **Major**: Significant quality issue, notable gap, or meaningful improvement. Should fix soon.
- **Minor**: Small improvement, polish item, or nice-to-have enhancement. Fix when convenient.
- **Observation**: Noteworthy pattern or insight that doesn't require action but is worth documenting.

**For the Strengths group, use different ratings:**
- **Strong**: Pattern is well-implemented, clearly beneficial, should be preserved as-is
- **Moderate**: Pattern works but has minor gaps or could be reinforced
- **Needs Reinforcement**: Good intent but implementation is incomplete or at risk of erosion

For strengths, the "recommendation" should be a **preservation recommendation**: what to protect, what NOT to change, and optionally how to reinforce.

**Output:** 5 response files with detailed, evidence-based analysis.

---

## Validation Phase

**Goal:** Validate all feedback against actual file state, discard hallucinations, and produce a prioritized final report.

Launch **5 subagents IN PARALLEL**, one per response file.

**Each subagent prompt must include:**
- Instruction to read their assigned `feedbacks/<timestamp>_<topic-slug>/feedback_response_group_<N>_*.md` file
- Instruction to RE-READ the actual target files to VERIFY each claim made in the response
- **Validation criteria -- DISCARD items that:**
  - Reference content that does not exist in the actual files (hallucinated)
  - Make claims contradicted by the actual file state
  - Are internally incoherent or logically inconsistent
  - Duplicate another item in the same group (keep the better-articulated version)
- **For validated items, confirm or adjust:**
  - Severity rating (upgrade/downgrade if warranted based on re-reading)
  - Recommendation specificity (ensure it references real file paths and content)
  - Implementation effort estimate: `Low` (< 30 min) / `Medium` (1-3 hours) / `High` (> 3 hours). Effort assumes implementation by a single agent session.
- Output a validated, prioritized list for their group
- Reminder: Do NOT modify any target files

**For the Strengths group:**
- Verify the claimed strength actually exists in the file
- Confirm it's genuinely effective (not just present)
- Check if any pending issues from other groups would undermine this strength
- Adjust rating if the strength is at risk from proposed changes

**Output:** Collect validated feedback from all 5 subagents.

---

## Final Compilation

After all Validation Phase subagents complete, compile the results into:
```
feedbacks/<timestamp>_<topic-slug>/final_feedback.md
```

**Format:**

```markdown
# Feedback Pipeline Results

**Target:** $ARGUMENTS
**Date:** [current date]
**Initiated By**: [user / orchestrator / retrospective-agent / other]

## Summary

**What was analyzed:** [1-2 sentence description of the target - e.g., "The CLAUDE.md orchestrator configuration file (676 lines) defining agent roles, workflows, and rules."]

**Why:** [1-2 sentence description of the improvement goal - e.g., "To identify structural issues, content gaps, and workflow risks while preserving effective patterns."]

**Key findings:** [3-5 bullet summary of the most important discoveries]
- [finding 1]
- [finding 2]
- [finding 3]

---

**Statistics:**
- Total items analyzed: [count]
- Items validated: [count after discarding]
- Items discarded: [count]
- Strengths identified: [count]

---

## Critical Issues

[Items rated Critical, sorted by implementation effort (Low first)]

For each item:
### [Title]
- **Source group:** [group name]
- **Finding:** [what was found]
- **Evidence:** [specific file path + line/section reference]
- **Recommendation:** [concrete action]
- **Effort:** Low / Medium / High

---

## Major Issues

[Same format as Critical]

---

## Minor Issues

[Same format as Critical]

---

## Observations

[Same format but without Effort rating]

---

## Strengths to Preserve

[Items from the Strengths group, sorted by rating (Strong first)]

For each item:
### [Title]
- **Rating:** Strong / Moderate / Needs Reinforcement
- **What works:** [description of the effective pattern]
- **Evidence:** [specific file path + line/section reference]
- **Preservation recommendation:** [what to protect, what NOT to change]
- **Risk from proposed changes:** [any items above that might undermine this strength, or "None"]

---

## Implementation Roadmap

### Immediate (Critical + Low Effort)
- [ ] [item]

### Short-term (Critical + Medium/High Effort, Major + Low Effort)
- [ ] [item]

### Medium-term (Major + Medium/High Effort, Minor + Low Effort)
- [ ] [item]

### Backlog (Minor + Medium/High Effort, Observations)
- [ ] [item]

### Preserve (Strengths - do NOT change)
- [ ] [strength item] — Protect during implementation of [related issue]

---

## Discarded Items

| # | Original Claim | Reason for Discarding |
|---|---------------|----------------------|
| 1 | [claim] | [reason: hallucinated / contradicted / incoherent / duplicate] |
```

### Conflict Check

After generating the final report, scan other `feedbacks/*/final_feedback.md` files for potential conflicts:
- Items touching the same files or sections
- Contradictory recommendations
- Items that might supersede existing pending items

Add a `## Potential Conflicts` section to the final report if any are found.

**Note:** This check is REQUIRED when the target includes `CLAUDE.md`, `.claude/`, or `prompt-templates/`. For other targets, it is optional but recommended.

---

## Execution Notes

- Always create `pipeline_input.md` FIRST before any analysis begins. This ensures the original request is preserved even if the pipeline is interrupted.
- All subagent phases (Ideation, Analysis, Validation) use **parallel execution** for efficiency.
- If "$ARGUMENTS" is vague, ask the user to clarify the exact files or system to analyze before starting.
- If the target involves more than 20 files, consider narrowing scope or grouping files into logical units for subagents.
- Subagents must always cite specific file paths and content -- never make claims without evidence.
- The pipeline is READ-ONLY with respect to target files. Only the `feedbacks/<timestamp>_<topic-slug>/` subfolder is written to.
- The subfolder name combines a timestamp with a topic slug derived from the user's input (e.g., "the CLAUDE.md and agent templates" at 14:30 becomes `feedbacks/2026-01-25T14-30-00_claude-md-and-agent-templates/`).
- If a subagent fails or produces empty output, note it in the final report and continue with available results.
- Strengths are as important as problems. A feedback pipeline that only finds flaws misses half the picture. Preserve what works while fixing what doesn't.
