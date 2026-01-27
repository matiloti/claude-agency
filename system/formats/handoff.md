# Handoff Format

Standard format for agent-to-agent handoffs.

---

## Handoff Template

```markdown
# Handoff: [feature name]

- **From**: [agent role]
- **To**: [target agent role]
- **Date**: [ISO 8601 date]

## What Was Completed
- [list of completed work items]

## Key Decisions
- [decisions that affect downstream work]

## Files Created/Modified
- [list of relevant files with brief descriptions]

## API Contracts / Interfaces
- [any contracts the downstream agent must conform to]
- [endpoint specifications, data formats, etc.]

## Notes for Next Agent
- [anything the next agent should know]
- [gotchas, assumptions, dependencies]
```

---

## Extended Handoff (Backend to Frontend)

For backend-to-frontend handoffs, include additional sections:

```markdown
## API Documentation

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | /api/v1/resource | List resources |
| POST | /api/v1/resource | Create resource |

### Request/Response Examples

[Include curl examples or JSON samples]

## TypeScript Interfaces

[Provide interface definitions for frontend]

## Error Handling

[Document error codes and expected handling]

## Development Setup

[Any setup instructions for testing against the API]
```

---

## File Location

Handoffs are stored in: `knowledge/handoffs/`

Filename format: `{from-agent}-to-{to-agent}-{feature}.md`

Example: `knowledge/handoffs/kotlin-spring-boot-backend-developer-to-react-native-expo-frontend-developer-auth.md`

---

## Parallel Work Handoffs

For parallel tracks (Backend/Frontend):
- Handoffs are created upon completion of each track
- The second-to-complete agent should read the first agent's handoff before finalizing
- This ensures interface compatibility between parallel implementations
