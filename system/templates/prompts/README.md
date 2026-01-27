# Prompt Templates - Variable Reference

This document provides the authoritative reference for all template variables used by the orchestrator when spawning subagents.

## Variable Definitions

### Common Variables (All Templates)

| Variable | Type | Description | Example |
|----------|------|-------------|---------|
| `{spawned_by}` | String | Who initiated this task. Uses controlled vocabulary from CLAUDE.md "Spawned By Vocabulary" section. | `orchestrator`, `user`, `judge`, `scheduled:retrospective-3-features` |
| `{list of relevant knowledge file paths}` | Multi-line list | Absolute or relative paths to knowledge files relevant to this task. Include architecture specs, user stories, previous handoffs. | `- knowledge/architecture/api/decks-api.md`<br>`- knowledge/user-stories/deck-management-stories.md` |

### Backend Developer Variables

| Variable | Example |
|----------|---------|
| `{list of relevant knowledge file paths}` | `- knowledge/architecture/api/decks-api.md`<br>`- knowledge/architecture/database/schema.sql`<br>`- knowledge/handoffs/architect-to-backend-developer-deck-management.md` |
| `{what to implement}` | `Implement the Decks CRUD API: POST /api/v1/decks, GET /api/v1/decks, GET /api/v1/decks/{id}, PUT /api/v1/decks/{id}, DELETE /api/v1/decks/{id}. Follow the API spec in knowledge/architecture/api/decks-api.md.` |
| `{resource}` (in references) | `decks` |

### Frontend Developer Variables

| Variable | Example |
|----------|---------|
| `{list of relevant knowledge file paths}` | `- knowledge/architecture/components/deck-list.md`<br>`- knowledge/architecture/api/decks-api.md`<br>`- knowledge/handoffs/backend-developer-to-frontend-developer-deck-management.md` |
| `{what to implement}` | `Implement the Deck List screen showing all user decks with pull-to-refresh, empty state, and navigation to deck detail.` |
| `{component}` (in references) | `deck-list` |
| `{resource}` (in references) | `decks` |

### Infrastructure Engineer Variables

| Variable | Example |
|----------|---------|
| `{list of relevant knowledge file paths}` | `- knowledge/infrastructure/status/current-state.md`<br>`- knowledge/architecture/database/schema.sql` |
| `{what to fix, configure, or set up}` | `Add health check to the Spring Boot container and configure proper wait-for-db behavior in docker-compose.yml.` |

### Architect Variables

| Variable | Example |
|----------|---------|
| `{list of relevant knowledge file paths}` | `- knowledge/ideas/deck-management-idea.md`<br>`- knowledge/user-stories/deck-management-stories.md`<br>`- knowledge/personas/student-persona.md` |
| `{user stories or features to architect}` | `Design the API and database schema for the Deck Management feature as described in the user stories.` |

### Ideator Variables

| Variable | Example |
|----------|---------|
| `{list of relevant knowledge file paths}` | `- knowledge/features/priority-list.md`<br>`- knowledge/personas/student-persona.md` |
| `{description of what to ideate on}` | `The user wants a "study session" feature where they can review cards in a deck using spaced repetition. Refine this idea into concrete user stories.` |

### QA Tester Variables

| Variable | Example |
|----------|---------|
| `{list of relevant knowledge file paths}` | `- knowledge/architecture/api/decks-api.md`<br>`- knowledge/user-stories/deck-management-stories.md`<br>`- knowledge/backend/status/deck-management-status.md`<br>`- knowledge/frontend/status/deck-management-status.md` |
| `{what to test}` | `Test the complete Deck Management feature: CRUD operations, error handling, empty states, and API contract compliance.` |
| `{epic}` (in references) | `deck-management` |
| `{resource}` (in references) | `decks` |
| `{feature}` (in references) | `deck-management` |

### Judge Variables

| Variable | Example |
|----------|---------|
| `{agent name}` | `backend-developer` |
| `{original task description}` | `Implement the Decks CRUD API following the spec in knowledge/architecture/api/decks-api.md` |
| `{agent's completion summary}` | (paste the agent's full completion output) |
| `{list of relevant knowledge file paths}` | `- knowledge/architecture/api/decks-api.md`<br>`- knowledge/architecture/database/schema.sql`<br>`- knowledge/user-stories/deck-management-stories.md` |
| `{list of files/artifacts produced by the agent}` | `- flashcards-backend/src/main/kotlin/com/flashcards/controller/DeckController.kt`<br>`- flashcards-backend/src/test/kotlin/com/flashcards/controller/DeckControllerTest.kt`<br>`- knowledge/backend/status/deck-management-status.md` |

## Relevance Rules

When selecting which knowledge files to include in `{list of relevant knowledge file paths}`:

1. **Always include**: The specific architectural artifacts for the feature (API spec, schema, component plan).
2. **Include if exists**: Previous handoff files from upstream agents.
3. **Include if exists**: Previous Judge reviews for revision tasks.
4. **Include for context**: User stories and personas relevant to the feature.
5. **Include for reference**: `knowledge/reviews/common-issues.md` (helps agents avoid known pitfalls).
6. **Include for revision**: Original task prompt + previous output + Judge feedback.

## Revision Prompt Template

When constructing a revision prompt (after NEEDS_REVISION verdict), use this structure:

```markdown
## Revision Task for {agent_role}

### Original Task
{paste the original task description}

### Previous Output
{paste the agent's completion summary}

### Judge Feedback
{paste the full Judge review}

### Knowledge Files
{list all relevant knowledge file paths}

### Revision Instructions
Address ALL issues flagged by the Judge:
1. {issue 1 from Judge review}
2. {issue 2 from Judge review}
...

### Constraints
- Do NOT redo approved work.
- Focus only on the issues above.
- Run pre-submission checks before reporting completion.
- Create new fix commits (do not amend).

### Session Log
(standard session log instruction)
```
