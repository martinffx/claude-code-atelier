---
name: api-design
description: REST API design conventions and best practices. Use when designing endpoints, naming resources, choosing HTTP methods, structuring responses, handling errors, or planning API versioning.
user-invocable: false
---

# API Design Principles

Consistent, predictable REST APIs.

## Resource Naming

```
# Plural nouns for collections
GET  /users
GET  /users/{id}
POST /users

# Nested resources for relationships
GET  /users/{id}/posts
POST /users/{id}/posts

# Flat when relationship is weak or queryable
GET  /posts?author={userId}
```

### Naming Rules
- Plural nouns: `/users` not `/user`
- Lowercase with hyphens: `/order-items` not `/orderItems`
- No verbs in paths: `/users/{id}/activate` → `POST /users/{id}/activation`
- No trailing slashes: `/users` not `/users/`
- Max 3 levels deep: `/users/{id}/posts` not `/users/{id}/posts/{id}/comments/{id}/likes`

## HTTP Methods

| Method | Use | Idempotent | Body |
|--------|-----|------------|------|
| GET | Read resource(s) | Yes | No |
| POST | Create resource | No | Yes |
| PUT | Replace resource entirely | Yes | Yes |
| PATCH | Partial update | Yes* | Yes |
| DELETE | Remove resource | Yes | No |

*PATCH is idempotent if applying same patch yields same result.

### Method Selection
```
Create new resource         → POST /resources
Get single resource         → GET /resources/{id}
Get collection              → GET /resources
Replace entire resource     → PUT /resources/{id}
Update specific fields      → PATCH /resources/{id}
Delete resource             → DELETE /resources/{id}
Trigger action              → POST /resources/{id}/actions/{action}
```

## Status Codes

### Success (2xx)
| Code | When |
|------|------|
| 200 | GET success, PATCH success, DELETE with body |
| 201 | POST created (include Location header) |
| 204 | DELETE success, PUT success (no body) |

### Client Error (4xx)
| Code | When |
|------|------|
| 400 | Malformed request, validation failed |
| 401 | Not authenticated |
| 403 | Authenticated but not authorized |
| 404 | Resource not found |
| 409 | Conflict (duplicate, state conflict) |
| 422 | Valid syntax but semantic error |
| 429 | Rate limited |

### Server Error (5xx)
| Code | When |
|------|------|
| 500 | Unexpected server error |
| 502 | Bad gateway (upstream failed) |
| 503 | Service unavailable (maintenance, overload) |

## Error Responses

Consistent structure for all errors:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address"
      }
    ]
  }
}
```

### Error Codes
- Machine-readable: `USER_NOT_FOUND`, `INSUFFICIENT_BALANCE`
- Stable across versions
- Documentable and searchable

## Pagination

### Cursor-based (preferred)
```
GET /posts?limit=20&cursor=eyJpZCI6MTAwfQ

Response:
{
  "items": [...],
  "nextCursor": "eyJpZCI6MTIwfQ",
  "hasMore": true
}
```

### Offset-based (simple cases only)
```
GET /posts?limit=20&offset=40

Response:
{
  "items": [...],
  "total": 253,
  "limit": 20,
  "offset": 40
}
```

Cursor > offset when:
- Large datasets
- Real-time data (items added/removed)
- Performance matters

## Filtering & Sorting

```
# Filtering
GET /posts?status=published&authorId=123

# Multiple values
GET /posts?status=draft,published

# Sorting
GET /posts?sort=createdAt:desc
GET /posts?sort=title:asc,createdAt:desc

# Date ranges
GET /posts?createdAfter=2024-01-01&createdBefore=2024-12-31

# Search
GET /posts?q=typescript
```

## Request/Response Patterns

### Create (POST)
```
Request:
POST /users
{
  "name": "Alice",
  "email": "alice@example.com"
}

Response: 201 Created
Location: /users/123
{
  "id": "123",
  "name": "Alice",
  "email": "alice@example.com",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

### Update (PATCH)
```
Request:
PATCH /users/123
{
  "name": "Alice Smith"
}

Response: 200 OK
{
  "id": "123",
  "name": "Alice Smith",
  "email": "alice@example.com",
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-16T14:00:00Z"
}
```

### Bulk Operations
```
POST /users/bulk
{
  "operations": [
    { "action": "create", "data": { "name": "Bob" } },
    { "action": "update", "id": "123", "data": { "name": "Alice" } },
    { "action": "delete", "id": "456" }
  ]
}

Response: 200 OK
{
  "results": [
    { "success": true, "id": "789" },
    { "success": true, "id": "123" },
    { "success": false, "error": { "code": "NOT_FOUND" } }
  ]
}
```

## Versioning

### URL path (recommended for breaking changes)
```
/v1/users
/v2/users
```

### Header (for minor variations)
```
Accept: application/vnd.api+json; version=2
```

### When to version
- Breaking: Field removed, type changed, required field added → New version
- Non-breaking: Field added, optional field → Same version

## Timestamps

Always ISO 8601 with timezone:
```json
{
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-16T14:00:00.123Z"
}
```

## IDs

- Prefer UUIDs for public APIs (no enumeration)
- Strings even if numeric internally: `"id": "123"` not `"id": 123`
- Consistent naming: `userId`, `postId` for foreign keys

## Expanding Relations

```
# Default: IDs only
GET /posts/123
{ "authorId": "456" }

# Expanded
GET /posts/123?expand=author
{
  "authorId": "456",
  "author": {
    "id": "456",
    "name": "Alice"
  }
}

# Multiple expansions
GET /posts/123?expand=author,comments
```

## Rate Limiting Headers

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1704067200
Retry-After: 60  (on 429)
```

## Guidelines Summary

1. Resources are nouns, actions are HTTP methods
2. Consistent pluralization and casing
3. Status codes match semantics
4. Error format is predictable and machine-readable
5. Pagination on all list endpoints
6. Cursor pagination for scale
7. Version when breaking, extend when not
8. ISO 8601 timestamps, string IDs
9. Document everything that isn't obvious
