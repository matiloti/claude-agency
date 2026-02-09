# Security Checklist

Shared security checklist for judges to reference during code reviews. Any security issue is grounds for automatic rejection.

---

## Authentication & Authorization

### Authentication

- [ ] Every non-public endpoint requires authentication
- [ ] Authentication tokens validated on every request
- [ ] No endpoint accessible without proper auth (unless explicitly documented as public)
- [ ] Auth tokens have appropriate expiration
- [ ] Token refresh mechanism implemented correctly

### Authorization

- [ ] Every endpoint has authorization check (not just authentication)
- [ ] User permissions verified for the specific resource/action
- [ ] Role-based access control enforced where applicable
- [ ] Authorization checks happen at service layer (not just controller)

---

## IDOR Protection (Insecure Direct Object Reference)

- [ ] Users can only access their own resources
- [ ] Resource ownership verified before ANY operation (read, update, delete)
- [ ] No ID enumeration vulnerabilities (can't increment IDs to access other users' data)
- [ ] Composite keys include user context where appropriate
- [ ] Sub-resources verify parent ownership chain

**Example of vulnerable code:**
```kotlin
// VULNERABLE - No ownership check
fun getOrder(orderId: Long) = orderRepository.findById(orderId)

// SAFE - Ownership verified
fun getOrder(orderId: Long, userId: Long) =
    orderRepository.findByIdAndUserId(orderId, userId)
        ?: throw NotFoundException("Order not found")
```

---

## SQL Injection Prevention

- [ ] ALL queries use parameterized statements
- [ ] No raw string concatenation in SQL queries
- [ ] Dynamic ORDER BY validated against allowlist (not interpolated)
- [ ] LIKE patterns properly escaped
- [ ] Table/column names never from user input
- [ ] Native queries reviewed for injection points

**Example of vulnerable code:**
```kotlin
// VULNERABLE - SQL injection
fun findByStatus(status: String) =
    jdbcTemplate.query("SELECT * FROM orders WHERE status = '$status'", mapper)

// SAFE - parameterized
fun findByStatus(status: String) =
    jdbcTemplate.query("SELECT * FROM orders WHERE status = ?", mapper, status)
```

**ORDER BY validation:**
```kotlin
// VULNERABLE - Direct interpolation
fun findAll(sortBy: String) =
    jdbcTemplate.query("SELECT * FROM orders ORDER BY $sortBy", mapper)

// SAFE - Allowlist validation
private val ALLOWED_SORT_COLUMNS = setOf("created_at", "amount", "status")

fun findAll(sortBy: String): List<Order> {
    val column = if (sortBy in ALLOWED_SORT_COLUMNS) sortBy else "created_at"
    return jdbcTemplate.query("SELECT * FROM orders ORDER BY $column", mapper)
}
```

---

## Input Validation

### General Validation

- [ ] All user inputs validated (length, format, range)
- [ ] Validation annotations on request DTOs (`@NotBlank`, `@Size`, `@Min`, `@Max`, etc.)
- [ ] Business rule validation in service layer
- [ ] Validation error messages don't leak implementation details

### Specific Validations

- [ ] String lengths bounded (prevent memory exhaustion)
- [ ] Numeric ranges validated (prevent overflow/underflow)
- [ ] Email/URL formats validated with proper regex
- [ ] Date/time formats validated
- [ ] Enum values validated against allowed set
- [ ] File uploads validated (type, size, content)

**Example:**
```kotlin
data class CreateOrderRequest(
    @field:NotBlank(message = "Product name is required")
    @field:Size(max = 200, message = "Product name must be under 200 characters")
    val productName: String,

    @field:Min(value = 1, message = "Quantity must be at least 1")
    @field:Max(value = 1000, message = "Quantity cannot exceed 1000")
    val quantity: Int,

    @field:DecimalMin(value = "0.01", message = "Price must be positive")
    val price: BigDecimal
)
```

---

## Mass Assignment Prevention

- [ ] Request/Response DTOs separate from domain entities
- [ ] No automatic field mapping from request to domain without explicit control
- [ ] Sensitive fields (isAdmin, createdAt, userId) not settable via request
- [ ] DTOs explicitly define which fields are allowed

**Example of vulnerable code:**
```kotlin
// VULNERABLE - Maps all fields including isAdmin
@PostMapping("/users")
fun createUser(@RequestBody user: User) = userRepository.save(user)

// SAFE - Explicit field mapping
@PostMapping("/users")
fun createUser(@RequestBody request: CreateUserRequest): User {
    val user = User(
        name = request.name,
        email = request.email,
        // isAdmin intentionally not mapped - always defaults to false
    )
    return userRepository.save(user)
}
```

---

## Sensitive Data Handling

### Logging

- [ ] No passwords in logs (even masked)
- [ ] No tokens/secrets in logs
- [ ] No PII in logs (emails, phone numbers, addresses only if essential)
- [ ] No credit card numbers in logs
- [ ] Request/response logging sanitizes sensitive fields

### Responses

- [ ] Passwords never included in API responses
- [ ] Tokens only included when explicitly needed
- [ ] Internal IDs not exposed when external IDs available
- [ ] Stack traces not exposed in production error responses
- [ ] Sensitive query parameters not logged

### Storage

- [ ] Passwords hashed with bcrypt/argon2 (not MD5/SHA-1)
- [ ] API keys/tokens stored securely (not in plain text)
- [ ] Encryption at rest for highly sensitive data

---

## Rate Limiting

- [ ] Auth endpoints (login, register, password reset) rate limited
- [ ] Expensive operations rate limited (report generation, bulk operations)
- [ ] Rate limits documented in API specification
- [ ] Rate limit headers included in responses (X-RateLimit-*)

---

## CORS Configuration

- [ ] Appropriate origins specified (not wildcard `*` in production)
- [ ] Credentials allowed only for trusted origins
- [ ] Allowed methods appropriately restricted
- [ ] Allowed headers appropriately restricted

---

## Additional Security Checks

### Session Management

- [ ] Session tokens regenerated after login
- [ ] Session invalidation on logout
- [ ] Concurrent session handling considered

### Error Handling

- [ ] Consistent error response format (no information leakage)
- [ ] Generic error messages for auth failures (no user enumeration)
- [ ] Internal errors return 500 without stack traces
- [ ] Validation errors return 400 with field-specific (not SQL-specific) messages

### Headers

- [ ] Security headers configured (X-Content-Type-Options, X-Frame-Options, etc.)
- [ ] HTTPS enforced (redirect HTTP to HTTPS)
- [ ] Cookie flags set (HttpOnly, Secure, SameSite)

---

## Verdict Impact

| Issue Type | Verdict |
|------------|---------|
| SQL injection vulnerability | **REJECTED** |
| Authorization bypass (IDOR) | **REJECTED** |
| Missing authentication on sensitive endpoint | **REJECTED** |
| Sensitive data exposure in logs/responses | **REJECTED** |
| Missing input validation | **NEEDS_REVISION** |
| Missing rate limiting on auth endpoints | **NEEDS_REVISION** |
| Improper error message (leaks info) | **NEEDS_REVISION** |
| CORS too permissive | **NEEDS_REVISION** |
