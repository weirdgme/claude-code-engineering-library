# API Documentation

Guide to documenting REST APIs, GraphQL APIs, and generating interactive API documentation using OpenAPI/Swagger.

## REST API Documentation Template

```markdown
## POST /api/users

Create a new user account.

### Authentication
Requires API key:
```
Authorization: Bearer <token>
```

### Request
```json
{
  "email": "user@example.com",
  "name": "John Doe"
}
```

### Response (201)
```json
{
  "id": "usr_123",
  "email": "user@example.com",
  "name": "John Doe"
}
```

### Errors
- 400: Invalid input
- 401: Unauthorized
- 409: Email exists
```

## OpenAPI Example

```yaml
openapi: 3.0.0
info:
  title: API
  version: 1.0.0

paths:
  /users:
    post:
      summary: Create user
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UserInput'
      responses:
        '201':
          description: Created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
        email:
          type: string
```

## Best Practices

✅ Complete request/response examples
✅ Document all error codes
✅ Show authentication requirements
✅ Include curl examples
✅ Keep spec synced with code
✅ Use Swagger UI for interactive docs

---

**Related Resources:**
- openapi-specification.md - Complete OpenAPI guide
- technical-writing-guide.md - Writing style
