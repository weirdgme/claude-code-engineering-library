# OpenAPI Specification

Complete guide to OpenAPI 3.0 specification for API documentation.

## Basic Structure

```yaml
openapi: 3.0.0
info:
  title: My API
  version: 1.0.0
  description: API description
  contact:
    email: api@example.com

servers:
  - url: https://api.example.com/v1

paths:
  /users/{id}:
    get:
      summary: Get user by ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      required:
        - id
        - email
      properties:
        id:
          type: string
        email:
          type: string
          format: email

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []
```

## Generating Docs

```bash
# Swagger UI
npx swagger-ui-watcher openapi.yaml

# Redoc
npx redoc-cli serve openapi.yaml

# Generate static site
npx redoc-cli bundle openapi.yaml -o index.html
```

---

**Related Resources:**
- api-documentation.md - API documentation practices
