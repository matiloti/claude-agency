# Judge Review Checklists

Standard checklists for Judge reviews across all projects.

---

## Performance Review

For Backend Developer and Frontend Developer output:

- [ ] No N+1 query patterns (check repository methods for loops with queries)
- [ ] List endpoints have pagination parameters
- [ ] Database queries use appropriate indexes (check schema for WHERE/ORDER BY columns)
- [ ] No unbounded in-memory collections (no `findAll()` without limits)
- [ ] API response times are reasonable for the operation complexity
- [ ] Frontend: No unnecessary re-renders, proper memoization where needed
- [ ] Frontend: Lists use virtualization (FlatList, not ScrollView with map)

---

## Security Review

Go beyond surface-level checks. Verify:

- [ ] **Authorization on every endpoint**: Not just authentication — verify the user has permission for the specific resource
- [ ] **No mass assignment**: DTOs should not blindly map all request fields to domain objects
- [ ] **Parameterized ORDER BY**: Dynamic sort fields must be validated against an allowlist, not interpolated
- [ ] **No IDOR**: Resource access checks (user can only access their own resources)
- [ ] **Input validation**: Length limits, format validation, sanitization on all user inputs
- [ ] **Rate limiting considerations**: Note if missing on auth endpoints or expensive operations
- [ ] **No sensitive data in logs**: Passwords, tokens, PII not logged
- [ ] **CORS configuration**: Appropriate origins, not wildcard in production

---

## Test Quality Criteria

Tests must be **meaningful**. Specifically verify:

- [ ] Tests cover **business logic** (not just getters/setters or framework behavior)
- [ ] Tests cover **edge cases** (empty inputs, boundary values, max lengths)
- [ ] Tests cover **error paths** (invalid input, not found, unauthorized)
- [ ] Tests are **independent** (no test depends on another test's state)
- [ ] Tests use **descriptive names** that document the behavior being tested
- [ ] Integration tests use **real dependencies** (Testcontainers, not mocked DB)
- [ ] No test **tests the framework** (e.g., testing that Spring DI works)

---

## Integration Contract Verification

When reviewing Frontend or Backend independently:

- [ ] Frontend request shapes (URL, method, body, headers) match Backend controller expectations
- [ ] Frontend handles all response shapes the Backend can return (including error responses)
- [ ] Field names and types match between Frontend types and Backend DTOs
- [ ] Pagination parameters match between Frontend queries and Backend endpoints
- [ ] If Backend adds a required field, Frontend sends it

---

## Context7 Verification

- [ ] Agent listed "Documentation Consulted" in their completion summary
- [ ] Lookups are relevant to the libraries actually used
- [ ] If a non-obvious API pattern is used, verify it matches current docs
- [ ] If [UNVERIFIED] flag is present, manually verify documentation compliance
- [ ] Agent used no more than 3 Context7 calls (check if prioritization was appropriate)

---

## General Compliance Checklist

Used in every review:

- [ ] Matches architectural specification
- [ ] Follows coding standards
- [ ] Tests are adequate
- [ ] TDD/TBD practices followed
- [ ] No security concerns
- [ ] Documentation is sufficient
