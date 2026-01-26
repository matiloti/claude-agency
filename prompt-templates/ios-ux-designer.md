## Task for Frontend UX/UI Designer Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Design Request
{what to design or analyze}

### Reference Materials
- User stories: `knowledge/user-stories/{epic}/`
- Existing design system: `knowledge/frontend/design-system/tokens.md`
- Component documentation: `knowledge/frontend/components/`
- Architecture plans: `knowledge/architecture/components/`

### Requirements
- Invoke the `ios-ux-design` skill for iOS HIG analysis and interaction patterns
- Invoke the `mobile-ios-design` skill for SwiftUI implementation patterns
- Follow Apple Human Interface Guidelines
- Ensure accessibility compliance (VoiceOver, Dynamic Type, contrast ratios)
- Use semantic colors (not hardcoded RGB/hex values)
- Specify SF Symbols by name (not custom icons when system icons exist)
- Define all states (loading, empty, error, success)
- Document touch targets (minimum 44x44pt)

### Skill Usage
You MUST invoke the following skills:

1. **`ios-ux-design`**: For comprehensive iOS HIG analysis, accessibility requirements, navigation patterns, and interaction specifications
2. **`mobile-ios-design`**: For SwiftUI code patterns, layout specifications, and component templates

Strategy:
- For UI/UX analysis: Invoke `ios-ux-design` first for deep analysis, then `mobile-ios-design` for implementation patterns
- For new feature design: Use both skills to ensure HIG compliance and provide implementable specs
- For quick HIG review: Invoke `ios-ux-design` alone

### Escalation
If you encounter a blocker (unclear requirements, missing user research, conflicting feedback):
- STOP — do NOT proceed with assumptions
- Document the blocker in your completion summary under "Blockers Encountered"
- Complete what you CAN without the blocked work
- Report status as `blocked`

### Pre-Submission
Before reporting completion:
1. Verify skills were invoked and guidance applied
2. Verify all colors use semantic names or define 4 variants (light, dark, high-contrast variants)
3. Verify all typography uses iOS text styles
4. Verify touch targets are 44x44pt minimum
5. Verify accessibility requirements are documented
6. Complete the self-review checklist from your agent spec

### Expected Output
- Design specifications in `knowledge/frontend/design/`
- Accessibility reports in `knowledge/frontend/design/accessibility/` (if applicable)
- HIG compliance reports in `knowledge/frontend/design/hig-compliance/` (if applicable)
- Design system token updates in `knowledge/frontend/design-system/` (if new tokens)
- Respond with your completion summary following your output format

### Session Log
As your LAST action before completing, create a session log file at:
`sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_ios-ux-designer_<short-task-slug>.md`

Use the current date and time. The file must contain:
- **Timestamp**: Current ISO 8601 timestamp
- **Agent**: ios-ux-designer
- **Spawned By**: [value provided by orchestrator in task prompt]
- **Task**: Brief description of the task you were assigned
- **Status**: completed / blocked / needs-revision
- **Work Summary**: 2-5 sentences on what you accomplished
- **Artifacts Created/Modified**: List of files you created or changed
- **Key Decisions**: Any design decisions you made
- **Skills Invoked**: Which skills were used and key insights gained
- **Blockers Encountered**: Any blockers (or "None")
- **Outcome**: Final result
