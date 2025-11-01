# REST API Design

Guide to designing RESTful APIs following best practices.

## RESTful Principles

```
Resources (nouns, not verbs):
✅ GET /users/123
✅ POST /orders
❌ GET /getUser/123
❌ POST /createOrder

HTTP Methods:
GET    - Retrieve resource
POST   - Create resource
PUT    - Update entire resource
PATCH  - Partial update
DELETE - Remove resource
```

## Resource Modeling

```
Collection: /users
Resource:   /users/123
Sub-resource: /users/123/orders
```

## HTTP Status Codes

```
200 OK - Successful GET, PUT, PATCH
201 Created - Successful POST
204 No Content - Successful DELETE
400 Bad Request - Invalid input
401 Unauthorized - Missing/invalid auth
403 Forbidden - Authenticated but not authorized
404 Not Found - Resource doesn't exist
409 Conflict - Resource conflict (e.g., duplicate email)
422 Unprocessable Entity - Validation errors
429 Too Many Requests - Rate limit exceeded
500 Internal Server Error - Server error
```

## Request/Response Examples

```typescript
// POST /users (Create)
Request:
{
  "email": "user@example.com",
  "name": "John Doe"
}

Response (201 Created):
{
  "id": "usr_123",
  "email": "user@example.com",
  "name": "John Doe",
  "created_at": "2024-01-15T10:30:00Z"
}

// GET /users?page=1&limit=20 (List with pagination)
Response (200 OK):
{
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "pages": 8
  }
}

// PATCH /users/123 (Partial update)
Request:
{
  "name": "Jane Doe"
}

Response (200 OK):
{
  "id": "usr_123",
  "email": "user@example.com",
  "name": "Jane Doe",
  "updated_at": "2024-01-15T11:00:00Z"
}
```

## Best Practices

✅ Use nouns for resources
✅ Plural resource names (/users, not /user)
✅ Use proper HTTP methods
✅ Return appropriate status codes
✅ Implement pagination
✅ Use filtering (?status=active)
✅ Consistent error format

---

**Related Resources:**
- api-versioning.md - API versioning
- rate-limiting.md - Rate limiting
