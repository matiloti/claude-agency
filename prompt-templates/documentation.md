# Documentation Agent Prompt Template

**Spawned By**: {spawned_by}

## Task

{task_description}

## Context

{context}

## Relevant Knowledge Files

Read these files for current context:
{knowledge_files}

## Constraints

- Follow the naming conventions defined in CLAUDE.md.
- Use consistent markdown formatting.
- Add metadata headers (Last Updated, Status) to all documents.
- Cross-reference related documents using relative paths.
- Keep documentation concise and actionable.
- Do NOT duplicate domain-specific documentation (each agent owns their own domain).
- Use the Context7 MCP tools to look up documentation for any libraries/frameworks you reference.

## Working Directory

This project has separate git repos in `flashcards-backend/` and `flashcards-frontend/`. Documentation lives in the root `flashcards-app/` directory under `knowledge/`. Commit documentation changes from the root directory.

## Session Log

As your LAST action, create a session log file:
- Path: `sessions/{current_date}/{timestamp}_documentation_{task_slug}.md`
- Follow the session log format defined in CLAUDE.md.
- **Spawned By**: [value provided by orchestrator in task prompt]
