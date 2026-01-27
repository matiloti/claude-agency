# Agent Review Criteria

Specific review criteria for each agent type.

---

## Ideator Output

- [ ] Are user stories specific and testable?
- [ ] Are personas realistic and based on real needs?
- [ ] Is the scope appropriate (not over-engineered)?
- [ ] Do features align with domain principles?
- [ ] Are acceptance criteria clear and measurable?

---

## Architect Output

- [ ] Is the architecture the simplest solution that works?
- [ ] Are API contracts complete and consistent?
- [ ] Is the database schema properly normalized?
- [ ] Are edge cases and error scenarios addressed?
- [ ] Does the design support TDD and TBD?
- [ ] Are performance implications considered (pagination, indexing)?
- [ ] Is the technology choice justified?

---

## Backend Developer Output

- [ ] Was TDD followed (tests written first)?
- [ ] Do tests cover happy paths AND edge cases?
- [ ] Is Spring JDBC used correctly (no raw string concatenation in queries)?
- [ ] Are Testcontainers tests properly configured?
- [ ] Is error handling consistent and informative?
- [ ] Are Kotlin idioms used appropriately?
- [ ] Is the code secure (input validation, parameterized queries, auth checks)?
- [ ] Are commits small and atomic (TBD compliance)?

---

## Frontend Developer Output

- [ ] Does the implementation match the architectural plan?
- [ ] Is the code clean, readable, and well-typed (TypeScript)?
- [ ] Are components small and focused (< 150 lines)?
- [ ] Is TailwindCSS/NativeWind used correctly and consistently?
- [ ] Are loading, error, and empty states handled?
- [ ] Is the app accessible (labels, contrast, touch targets)?
- [ ] Are tests meaningful and not just testing implementation details?
- [ ] Is iOS HIG compliance verified (when applicable)?

---

## iOS UX Designer Output

- [ ] Does the design follow iOS Human Interface Guidelines?
- [ ] Are touch targets appropriately sized (44pt minimum)?
- [ ] Is the navigation pattern clear and consistent?
- [ ] Are accessibility requirements addressed?
- [ ] Is the design system consistent with existing patterns?
- [ ] Are all states designed (loading, error, empty, success)?

---

## QA Tester Output

- [ ] Are test cases comprehensive enough?
- [ ] Do bug reports have clear reproduction steps?
- [ ] Are quality gates reasonable and measurable?
- [ ] Is the test strategy covering all critical paths?
- [ ] Are edge cases identified and tested?

---

## Infrastructure Engineer Output

- [ ] Is the Docker configuration production-ready?
- [ ] Are environment variables properly managed?
- [ ] Is the CI/CD pipeline complete and correct?
- [ ] Are security best practices followed (no secrets in code)?
- [ ] Is the deployment process documented?

---

## Documentation Agent Output

- [ ] Is the documentation accurate and up-to-date?
- [ ] Is the structure logical and navigable?
- [ ] Are code examples correct and tested?
- [ ] Is the documentation accessible to the target audience?

---

## Severity Definitions

| Severity | Definition | Verdict Impact |
|----------|------------|----------------|
| **CRITICAL** | Blocks release. Security vulnerabilities, data loss risks, crashes, fundamental design flaws | REJECTED |
| **MAJOR** | Must fix before feature complete. Broken functionality, missing error handling, test gaps | NEEDS_REVISION |
| **MINOR** | Should fix but doesn't block. Style inconsistencies, minor UX issues, documentation gaps | APPROVED (with notes) |
