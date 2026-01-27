# System Conventions

This folder contains standard conventions and coding standards used across all projects in the agency.

## Available Conventions

| Convention | File | Description |
|------------|------|-------------|
| Kotlin Spring | `kotlin-spring-conventions.md` | Project structure, coding standards, testing patterns for Kotlin/Spring Boot |
| API Response | `api-response-standards.md` | Standard API response formats, HTTP status codes, error handling |

## Usage

Agent specs reference these conventions instead of embedding the content:

```markdown
## Conventions

Follow standards defined in:
- `system/conventions/kotlin-spring-conventions.md`
- `system/conventions/api-response-standards.md`
```

This reduces agent spec length and ensures consistency across projects.

## Adding New Conventions

When adding a new convention:

1. Create `{topic}-conventions.md` in this folder
2. Include code examples and rationale
3. Reference related conventions
4. Update this README
5. Update relevant agent specs to reference the new convention
