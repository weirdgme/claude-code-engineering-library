# API Versioning

Guide to API versioning strategies and best practices.

## Versioning Strategies

### URI Versioning
```
GET /v1/users/123
GET /v2/users/123

Pros: Simple, clear
Cons: Pollutes URI space
```

### Header Versioning
```
GET /users/123
Accept: application/vnd.myapi.v1+json

Pros: Clean URIs
Cons: Less visible, harder to test
```

### Query Parameter
```
GET /users/123?version=1
GET /users/123?version=2

Pros: Simple
Cons: Easy to forget, not RESTful
```

## Semantic Versioning

```
MAJOR.MINOR.PATCH

v1.0.0 → v1.1.0: New feature (backward-compatible)
v1.1.0 → v2.0.0: Breaking change
v2.0.0 → v2.0.1: Bug fix
```

## Breaking vs Non-Breaking Changes

```
Breaking Changes (require new version):
- Removing endpoint
- Removing field
- Changing field type
- Changing response structure
- Renaming field

Non-Breaking (can be same version):
- Adding new endpoint
- Adding new optional field
- Adding new query parameter
- Deprecating (but not removing) field
```

## Deprecation Strategy

```typescript
// Mark deprecated fields
type User {
  id: ID!
  fullName: String!
  name: String! @deprecated(reason: "Use fullName instead")
}

// Add deprecation headers
res.setHeader('Deprecation', 'Sun, 01 Jan 2025 00:00:00 GMT');
res.setHeader('Link', '</v2/users>; rel="successor-version"');
```

## Best Practices

✅ Version from day one (v1)
✅ Support 2-3 versions simultaneously
✅ Give 6-12 months deprecation notice
✅ Document breaking changes clearly
✅ Use semantic versioning
✅ Provide migration guides

---

**Related Resources:**
- rest-api-design.md - API design principles
