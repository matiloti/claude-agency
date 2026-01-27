# Kotlin Spring Boot Conventions

Standard conventions for Kotlin Spring Boot projects across the agency.

---

## Project Structure

```
src/
  main/kotlin/com/{project}/
    config/          # Spring configuration classes
    controller/      # REST controllers
    service/         # Business logic services
    repository/      # JDBC repository implementations
    model/           # Domain models and DTOs
    exception/       # Custom exceptions and error handling
    security/        # Authentication and authorization
  main/resources/
    db/migration/    # Flyway SQL migrations
    application.yml  # Application configuration
  test/kotlin/com/{project}/
    controller/      # Controller integration tests
    service/         # Service unit tests
    repository/      # Repository integration tests (Testcontainers)
    integration/     # End-to-end integration tests
```

---

## Coding Standards

### Kotlin Idioms
- Use data classes for DTOs and domain models
- Prefer immutable properties (`val` over `var`)
- Use extension functions for utility operations
- Leverage null safety - avoid nullable types unless absolutely necessary
- Use sealed classes for error types
- Use coroutines only when async behavior is genuinely needed

### Naming Conventions
- Classes: PascalCase (`DeckService`, `CreateDeckRequest`)
- Functions: camelCase (`findByUserId`, `createDeck`)
- Constants: SCREAMING_SNAKE_CASE (`MAX_DECK_SIZE`)
- Packages: lowercase (`com.project.service`)

### Example Patterns

```kotlin
// Data class for request DTO
data class CreateDeckRequest(
    val name: String,
    val description: String? = null
)

// Sealed class for errors
sealed class DeckError {
    data class NotFound(val id: UUID) : DeckError()
    data class DuplicateName(val name: String) : DeckError()
    object Unauthorized : DeckError()
}

// Extension function
fun String.toSlug(): String = this.lowercase().replace(" ", "-")
```

---

## Testing Standards

### Test-Driven Development (TDD)
1. **Red**: Write a failing test that describes the desired behavior
2. **Green**: Write the minimum code to make the test pass
3. **Refactor**: Improve the code while keeping tests green

Every public method must have corresponding tests.

### Unit Test Pattern (Service Layer)

```kotlin
@ExtendWith(MockitoExtension::class)
class DeckServiceTest {
    @Mock lateinit var deckRepository: DeckRepository
    @InjectMocks lateinit var deckService: DeckService

    @Test
    fun `should create a new deck`() {
        // Given
        val request = CreateDeckRequest(name = "Kotlin Basics")
        whenever(deckRepository.save(any())).thenReturn(expectedDeck)

        // When
        val result = deckService.createDeck(request)

        // Then
        assertThat(result.name).isEqualTo("Kotlin Basics")
        verify(deckRepository).save(any())
    }
}
```

### Integration Test Pattern (Repository with Testcontainers)

```kotlin
@SpringBootTest
@Testcontainers
class DeckRepositoryIntegrationTest {
    companion object {
        @Container
        val postgres = PostgreSQLContainer("postgres:16-alpine")

        @DynamicPropertySource
        @JvmStatic
        fun configureProperties(registry: DynamicPropertyRegistry) {
            registry.add("spring.datasource.url", postgres::getJdbcUrl)
            registry.add("spring.datasource.username", postgres::getUsername)
            registry.add("spring.datasource.password", postgres::getPassword)
        }
    }

    @Autowired lateinit var deckRepository: DeckRepository

    @Test
    fun `should persist and retrieve a deck`() {
        // test implementation
    }
}
```

### Test Naming Convention
Use backtick method names with descriptive sentences:
- `should create a new deck`
- `should throw exception when deck not found`
- `should return empty list when no decks exist`

### Test Structure
Follow Given-When-Then pattern:
- **Given**: Set up test data and mocks
- **When**: Execute the method under test
- **Then**: Assert results and verify interactions

---

## Spring JDBC Patterns

### Repository Pattern

```kotlin
@Repository
class DeckRepository(private val jdbcTemplate: NamedParameterJdbcTemplate) {

    fun findById(id: UUID): Deck? {
        val sql = "SELECT * FROM decks WHERE id = :id"
        val params = mapOf("id" to id)
        return jdbcTemplate.query(sql, params, deckRowMapper).firstOrNull()
    }

    fun save(deck: Deck): Deck {
        val sql = """
            INSERT INTO decks (id, name, user_id, created_at)
            VALUES (:id, :name, :userId, :createdAt)
        """.trimIndent()
        val params = mapOf(
            "id" to deck.id,
            "name" to deck.name,
            "userId" to deck.userId,
            "createdAt" to deck.createdAt
        )
        jdbcTemplate.update(sql, params)
        return deck
    }

    private val deckRowMapper = RowMapper { rs, _ ->
        Deck(
            id = rs.getObject("id", UUID::class.java),
            name = rs.getString("name"),
            userId = rs.getObject("user_id", UUID::class.java),
            createdAt = rs.getTimestamp("created_at").toInstant()
        )
    }
}
```

### Query Safety
- Always use parameterized queries (`:param` syntax)
- Never concatenate user input into SQL strings
- Use `NamedParameterJdbcTemplate` for readability

---

## Flyway Migrations

### Naming Convention
`V{version}__{description}.sql`
- Version: Sequential number (e.g., `V001`, `V002`)
- Description: Snake_case description (e.g., `create_decks_table`)

### Example Migration

```sql
-- V001__create_decks_table.sql
CREATE TABLE decks (
    id UUID PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    user_id UUID NOT NULL REFERENCES users(id),
    created_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_decks_user_id ON decks(user_id);
```

---

## Related Documentation

- API Response Standards: `system/conventions/api-response-standards.md`
- PostgreSQL Best Practices: Invoke `supabase-postgres-best-practices` skill
