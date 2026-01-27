# API Response Standards

Standard API response formats for all backend services across the agency.

---

## Response Envelope

All API responses use a consistent envelope format.

### Success Response

```json
{
  "data": { },
  "timestamp": "2024-01-01T00:00:00Z"
}
```

- `data`: The response payload (object, array, or primitive)
- `timestamp`: ISO 8601 UTC timestamp of the response

### Error Response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable message",
    "details": []
  },
  "timestamp": "2024-01-01T00:00:00Z"
}
```

- `code`: Machine-readable error code (SCREAMING_SNAKE_CASE)
- `message`: Human-readable description
- `details`: Optional array of field-level errors or additional context

---

## HTTP Status Codes

### Success Codes

| Code | Meaning | Use Case |
|------|---------|----------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST that creates a resource |
| 202 | Accepted | Request accepted for async processing |
| 204 | No Content | Successful DELETE or update with no body |

### Client Error Codes

| Code | Meaning | Use Case |
|------|---------|----------|
| 400 | Bad Request | Validation errors, malformed request |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Authenticated but not authorized |
| 404 | Not Found | Resource does not exist |
| 409 | Conflict | Duplicate resource, state conflict |
| 422 | Unprocessable Entity | Valid syntax but semantic errors |
| 429 | Too Many Requests | Rate limit exceeded |

### Server Error Codes

| Code | Meaning | Use Case |
|------|---------|----------|
| 500 | Internal Server Error | Unexpected server error |
| 502 | Bad Gateway | Upstream service error |
| 503 | Service Unavailable | Service temporarily unavailable |

---

## Standard Error Codes

### Authentication & Authorization
- `UNAUTHORIZED` - Missing or invalid token
- `FORBIDDEN` - Insufficient permissions
- `TOKEN_EXPIRED` - JWT token has expired
- `TOKEN_INVALID` - JWT token is malformed

### Validation
- `VALIDATION_ERROR` - Request failed validation
- `MISSING_FIELD` - Required field not provided
- `INVALID_FORMAT` - Field format is incorrect

### Resources
- `NOT_FOUND` - Resource does not exist
- `ALREADY_EXISTS` - Resource already exists (409)
- `CONFLICT` - State conflict prevents operation

### Rate Limiting
- `RATE_LIMIT_EXCEEDED` - Too many requests

---

## Validation Error Details

For validation errors, include field-level details:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      },
      {
        "field": "password",
        "message": "Must be at least 8 characters"
      }
    ]
  },
  "timestamp": "2024-01-01T00:00:00Z"
}
```

---

## Pagination

### Request Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `page` | integer | 0 | Page number (0-indexed) |
| `size` | integer | 20 | Items per page (max 100) |
| `sort` | string | - | Sort field and direction (e.g., `createdAt,desc`) |

### Response Format

```json
{
  "data": {
    "content": [],
    "page": {
      "number": 0,
      "size": 20,
      "totalElements": 150,
      "totalPages": 8
    }
  },
  "timestamp": "2024-01-01T00:00:00Z"
}
```

### Cursor-Based Pagination (Alternative)

For large datasets or real-time data:

```json
{
  "data": {
    "items": [],
    "cursor": {
      "next": "eyJpZCI6MTAwfQ==",
      "hasMore": true
    }
  },
  "timestamp": "2024-01-01T00:00:00Z"
}
```

---

## Implementation Example (Kotlin)

### Global Exception Handler

```kotlin
@RestControllerAdvice
class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException::class)
    fun handleValidationException(ex: MethodArgumentNotValidException): ResponseEntity<ErrorResponse> {
        val details = ex.bindingResult.fieldErrors.map {
            ErrorDetail(field = it.field, message = it.defaultMessage ?: "Invalid value")
        }
        return ResponseEntity.badRequest().body(
            ErrorResponse(
                error = ErrorBody(
                    code = "VALIDATION_ERROR",
                    message = "Request validation failed",
                    details = details
                )
            )
        )
    }

    @ExceptionHandler(ResourceNotFoundException::class)
    fun handleNotFound(ex: ResourceNotFoundException): ResponseEntity<ErrorResponse> {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(
            ErrorResponse(
                error = ErrorBody(
                    code = "NOT_FOUND",
                    message = ex.message ?: "Resource not found"
                )
            )
        )
    }
}

data class ErrorResponse(
    val error: ErrorBody,
    val timestamp: Instant = Instant.now()
)

data class ErrorBody(
    val code: String,
    val message: String,
    val details: List<ErrorDetail> = emptyList()
)

data class ErrorDetail(
    val field: String,
    val message: String
)
```

---

## Related Documentation

- Kotlin Spring Conventions: `system/conventions/kotlin-spring-conventions.md`
