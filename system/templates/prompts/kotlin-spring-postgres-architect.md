## Task for Architect Agent

**Spawned By**: {spawned_by}

### Context
Read the following knowledge files for context:
{list of relevant knowledge file paths}

### Requirements
{user stories or features to architect}

### Constraints
- Backend: Kotlin + Spring Boot + Spring JDBC + PostgreSQL.
- Frontend: React Native + NativeWind (TailwindCSS).
- Must support TDD (code must be testable with DI).
- Must support TBD (design for incremental delivery).
- Performance: APIs < 200ms, no N+1 queries, pagination on list endpoints.

### Schema Requirements
Database schemas MUST be executable (not just conceptual):
- ALL columns with exact types and constraints.
- ALL indexes (for WHERE, ORDER BY, JOIN columns).
- ALL foreign key constraints with explicit CASCADE rules.
- ALL CHECK constraints for enums or bounded values.
- DEFAULT values where applicable.
- The Backend Developer must be able to copy the schema directly into a Flyway migration.

### Documentation Lookup
- Use the context7 MCP tools (`resolve-library-id` then `query-docs`) to look up current documentation for any libraries, frameworks, or tools before designing. Always verify API capabilities and constraints against up-to-date docs.
- Prioritize: (1) Spring Boot capabilities, (2) PostgreSQL features, (3) React Native constraints.
- Max 3 Context7 calls — choose the most impactful lookups.

### Escalation
If you encounter a blocker (unclear requirements, conflicting constraints, missing info):
- STOP — do NOT proceed with assumptions on critical architectural decisions.
- Document the blocker in your completion summary under "Blockers Encountered."
- Complete what you CAN without the blocked decision.
- Report status as `blocked`.

### Expected Output
- Write architectural artifacts to `knowledge/architecture/`.
- Include API specs, database changes, and implementation plan.
- Create/update `knowledge/architecture/traceability-matrix.md` mapping user story criteria → API endpoints.
- If you notice refactoring opportunities, log them to `knowledge/backlog/tech-debt.md`.
- List "Documentation Consulted" (Context7 lookups) in your completion summary.
- Respond with your completion summary following your output format.

### References
- Check `knowledge/architecture/decisions/` for existing ADRs.
- Check `knowledge/architecture/database/schema.sql` for current schema.
- Check `knowledge/architecture/api/` for existing API specs.
- Check `knowledge/architecture/traceability-matrix.md` for existing mappings.

### Session Log
As your LAST action, create a session log following the format in `system/formats/session-log.md`.

**File path**: `sessions/YYYY-MM-DD/YYYY-MM-DDThh-mm-ss_kotlin-spring-postgres-architect_<short-task-slug>.md`
