# Database Checklist

Shared database checklist for judges to reference during code and architecture reviews. Covers query patterns, schema design, and data integrity.

---

## N+1 Query Prevention

The N+1 problem occurs when code fetches a list of N items, then executes 1 additional query for each item.

### Detection

- [ ] No loops containing database queries
- [ ] Repository methods don't call other repository methods in loops
- [ ] Service methods don't iterate over collections making individual DB calls
- [ ] Related data fetched with JOINs or batch queries (IN clause)

### Examples

**N+1 Problem:**
```kotlin
// PROBLEM - Executes N+1 queries (1 for users, N for orders)
fun getUsersWithOrders(): List<UserWithOrders> {
    val users = userRepository.findAll()
    return users.map { user ->
        UserWithOrders(user, orderRepository.findByUserId(user.id)) // Query per user!
    }
}
```

**Correct Solutions:**
```kotlin
// SOLUTION 1 - Single query with JOIN
fun getUsersWithOrders(): List<UserWithOrders> =
    jdbcTemplate.query("""
        SELECT u.*, o.* FROM users u
        LEFT JOIN orders o ON o.user_id = u.id
    """, userWithOrdersMapper)

// SOLUTION 2 - Batch fetch with IN clause
fun getUsersWithOrders(): List<UserWithOrders> {
    val users = userRepository.findAll()
    val userIds = users.map { it.id }
    val ordersByUser = orderRepository.findByUserIdIn(userIds)
        .groupBy { it.userId }

    return users.map { user ->
        UserWithOrders(user, ordersByUser[user.id] ?: emptyList())
    }
}
```

### Audit Process

1. Identify all repository/DAO methods
2. For each service method, trace database calls
3. Check for patterns: `findAll()` followed by `.map { repo.findBy...() }`
4. Verify related entity fetching uses JOINs or batch queries

---

## Pagination Requirements

All list endpoints MUST support pagination to prevent memory exhaustion and slow responses.

### API Requirements

- [ ] All list endpoints accept pagination parameters (page/size or cursor/limit)
- [ ] Pagination has reasonable defaults (e.g., page=0, size=20)
- [ ] Maximum page size enforced (e.g., max 100 items)
- [ ] Response includes pagination metadata (total count, has more, next cursor)

### Repository Requirements

- [ ] No unbounded `findAll()` without limits
- [ ] Paginated queries use database-level LIMIT/OFFSET (not in-memory slicing)
- [ ] Total count queries are optional (expensive for large tables)

### Examples

**API Contract:**
```kotlin
// Request parameters
data class PageRequest(
    val page: Int = 0,
    val size: Int = 20
) {
    init {
        require(page >= 0) { "Page must be non-negative" }
        require(size in 1..100) { "Size must be between 1 and 100" }
    }
}

// Response wrapper
data class PageResponse<T>(
    val content: List<T>,
    val page: Int,
    val size: Int,
    val totalElements: Long,
    val totalPages: Int,
    val hasNext: Boolean
)
```

**Repository Implementation:**
```kotlin
// CORRECT - Database-level pagination
fun findByUserId(userId: Long, page: Int, size: Int): List<Order> =
    jdbcTemplate.query(
        "SELECT * FROM orders WHERE user_id = ? ORDER BY created_at DESC LIMIT ? OFFSET ?",
        orderMapper, userId, size, page * size
    )

// INCORRECT - In-memory pagination (fetches all then slices)
fun findByUserId(userId: Long, page: Int, size: Int): List<Order> =
    jdbcTemplate.query("SELECT * FROM orders WHERE user_id = ?", orderMapper, userId)
        .drop(page * size)
        .take(size)  // DON'T DO THIS
```

---

## Indexing Requirements

Indexes are critical for query performance. Missing indexes cause full table scans.

### Required Indexes

- [ ] All columns in WHERE clauses have indexes
- [ ] All columns in ORDER BY clauses have indexes
- [ ] All columns in JOIN conditions have indexes (foreign keys)
- [ ] Composite indexes for multi-column queries (column order matters)
- [ ] Unique constraints where business rules require

### Index Guidelines

- [ ] Foreign key columns always indexed
- [ ] Columns used in pagination sorting indexed
- [ ] High-cardinality columns for index selection
- [ ] Partial indexes for filtered queries where appropriate

### Examples

**Schema with proper indexes:**
```sql
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    status VARCHAR(20) NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Foreign key index (for JOINs and lookups by user)
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Status lookups
CREATE INDEX idx_orders_status ON orders(status);

-- Pagination by date
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);

-- Composite index for filtered pagination
CREATE INDEX idx_orders_user_status_created
    ON orders(user_id, status, created_at DESC);
```

### Verification Process

1. List all WHERE clause columns across all queries
2. List all ORDER BY columns
3. List all JOIN columns
4. Verify each has an appropriate index
5. Check if composite indexes would help common query patterns

---

## Data Types

Correct data types ensure data integrity and query performance.

### Primary Keys

- [ ] UUIDs preferred for primary keys (not auto-increment integers)
- [ ] UUIDs stored as native UUID type (not VARCHAR)

### Timestamps

- [ ] All timestamps use `TIMESTAMPTZ` (timestamp with time zone)
- [ ] Timestamps include timezone information in application layer
- [ ] created_at/updated_at columns present where appropriate

### Numeric Data

- [ ] Monetary values use `DECIMAL`/`NUMERIC` (not FLOAT)
- [ ] Precision specified for decimal columns
- [ ] Integer sizes appropriate (SMALLINT, INTEGER, BIGINT)

### String Data

- [ ] VARCHAR with appropriate length limits
- [ ] TEXT for unbounded content
- [ ] ENUM values use CHECK constraints (not just documentation)

### Examples

**Correct data types:**
```sql
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    quantity INTEGER NOT NULL DEFAULT 0,
    status VARCHAR(20) NOT NULL CHECK (status IN ('draft', 'active', 'archived')),
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

---

## Batch Operations

Bulk operations should use database-level batching, not loops.

### Requirements

- [ ] Bulk inserts use batch INSERT (not individual INSERTs in loop)
- [ ] Bulk updates use single UPDATE with IN clause (not individual UPDATEs)
- [ ] Bulk deletes use single DELETE with IN clause
- [ ] Batch size limits for very large operations (e.g., 1000 rows per batch)

### Examples

**Incorrect - Loop with individual inserts:**
```kotlin
// SLOW - N separate INSERT statements
fun saveAll(orders: List<Order>) {
    orders.forEach { order ->
        jdbcTemplate.update(
            "INSERT INTO orders (id, user_id, amount) VALUES (?, ?, ?)",
            order.id, order.userId, order.amount
        )
    }
}
```

**Correct - Batch insert:**
```kotlin
// FAST - Single batch INSERT
fun saveAll(orders: List<Order>) {
    jdbcTemplate.batchUpdate(
        "INSERT INTO orders (id, user_id, amount) VALUES (?, ?, ?)",
        orders.map { arrayOf(it.id, it.userId, it.amount) }
    )
}

// Or using VALUES with multiple rows
fun saveAll(orders: List<Order>) {
    if (orders.isEmpty()) return

    val values = orders.joinToString(",") { "(?, ?, ?)" }
    val params = orders.flatMap { listOf(it.id, it.userId, it.amount) }

    jdbcTemplate.update(
        "INSERT INTO orders (id, user_id, amount) VALUES $values",
        *params.toTypedArray()
    )
}
```

---

## Schema Design

### Normalization

- [ ] Schema properly normalized (at least 3NF)
- [ ] Denormalization documented with justification when used
- [ ] No redundant data storage without explicit reason

### Referential Integrity

- [ ] Foreign key constraints defined
- [ ] CASCADE/RESTRICT rules explicitly specified
- [ ] ON DELETE behavior documented and intentional

### Naming Conventions

- [ ] Lowercase snake_case for table names
- [ ] Lowercase snake_case for column names
- [ ] Consistent plural/singular for table names (pick one)
- [ ] Descriptive index names (idx_tablename_columnnames)

### Migration Safety

- [ ] Migrations are reversible (DOWN migration exists)
- [ ] No data loss (DROP only with explicit approval)
- [ ] Idempotent where possible (IF NOT EXISTS patterns)
- [ ] Large table operations use CONCURRENTLY

---

## Verdict Impact

| Issue Type | Verdict |
|------------|---------|
| N+1 query pattern | **NEEDS_REVISION** |
| Missing pagination on list endpoint | **NEEDS_REVISION** |
| Missing index on WHERE/ORDER BY/JOIN column | **NEEDS_REVISION** |
| Unbounded findAll() without limits | **NEEDS_REVISION** |
| Individual inserts in loop (batch needed) | **NEEDS_REVISION** |
| Wrong data type (FLOAT for money, VARCHAR for UUID) | **NEEDS_REVISION** |
| Missing foreign key constraints | **NEEDS_REVISION** |
| Destructive migration without rollback | **NEEDS_REVISION** |
