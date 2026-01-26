# Continuous Improvement Workflow

<!-- Last Updated: 2026-01-25 -->
<!-- Status: Current -->
<!-- Author: documentation-agent -->

The system learns from past work through a structured feedback processing pipeline. This enables safe, incremental evolution of orchestration rules, agent specs, and prompt templates.

## Feedback Processing Flow

Feedback arrives in the `feedbacks/` folder (organized by topic subfolders). Each item follows this lifecycle:

1. **Intake** - Orchestrator reads new feedback items from `feedbacks/`.
2. **Analysis** - Spawn Retrospective Agent to evaluate relevance, effort, risk, and priority.
3. **Approval** - Orchestrator presents analysis to user. User approves which items to implement.
4. **Snapshot** - Infrastructure Engineer takes a snapshot before changes (see Snapshot Mechanism below).
5. **Implementation** - Delegate to the appropriate agent(s) based on file ownership (see Role Boundaries in CLAUDE.md).
6. **Review** - Judge reviews the changes.
7. **Verification** - If issues arise post-implementation, trigger Rollback Flow.

See `feedbacks/README.md` for detailed guidance on feedback file formats, folder structure, and status tracking.

## Snapshot Mechanism

Before implementing ANY feedback or system-level change to the orchestration system (CLAUDE.md, agent specs, prompt templates, knowledge structure), a snapshot must be taken.

### Snapshot Structure

```
snapshots/
  YYYY-MM-DDThh-mm-ss_{change-slug}/
    CLAUDE.md
    claude/              (copy of .claude/)
    prompt-templates/    (copy of prompt-templates/)
    knowledge/           (copy of knowledge/)
    feedbacks/           (copy of feedbacks/)
    manifest.md          (what triggered the snapshot, what's being changed)
```

### Manifest Format

The `manifest.md` file in each snapshot describes the change:

```markdown
# Snapshot Manifest

- **Timestamp**: [ISO 8601]
- **Trigger**: [feedback item or learning that prompted this change]
- **Changes Planned**: [list of files/sections being modified]
- **Rollback Instructions**: To restore, copy all files from this snapshot directory back to their original locations, overwriting current versions.
```

### Snapshot Rules

- Snapshots are taken by the Infrastructure Engineer (file/system operations).
- Only keep the last 5 snapshots. When creating a new one, delete the oldest if count exceeds 5.
- Snapshot MUST be confirmed before implementation begins.
- If the needed snapshot is unavailable (deleted by the 5-snapshot limit), create a manual recovery plan using git history and session logs. Document in `knowledge/retrospectives/{issue}-manual-recovery.md`.

## Learning Collection Flow

After every 3 completed features (or on-demand), the Retrospective Agent runs a learning collection cycle:

1. **Analyze** - Review session logs, Judge reviews, common issues, and revision counts.
2. **Propose** - Produce improvement proposals in `knowledge/retrospectives/`.
3. **Present** - Orchestrator presents proposals to user for approval.
4. **Convert** - Approved proposals become new feedback items in `feedbacks/`.
5. **Process** - Feedback Processing Flow kicks in (snapshot, implement, review).

## System Change Rollback

When the user or system identifies that a recent change degraded system behavior:

1. **Identify** - Check `snapshots/` for the relevant snapshot (read `manifest.md` to find the right one).
2. **Restore** - Infrastructure Engineer copies files from snapshot back to their original locations.
3. **Document** - Create `knowledge/retrospectives/{issue}-rollback.md` explaining what went wrong.
4. **Mark** - The problematic feedback item is marked as `reverted` in its `status.md`.

## Feedback Lifecycle States

See `feedbacks/README.md` for Feedback Lifecycle States, status.md format, and implementation tracking details.

## Related Orchestration Rules

The following orchestration rules (from CLAUDE.md) relate to continuous improvement:

- **Rule 17**: After every 3 completed features, spawn the Retrospective Agent for learning collection.
- **Rule 18**: Before implementing any system-level change, ensure a snapshot is taken by the Infrastructure Engineer.
- **Rule 20**: Process feedback items from `feedbacks/` folder using this workflow. Never implement feedback without user approval and a snapshot.
