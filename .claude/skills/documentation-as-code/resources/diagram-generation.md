# Diagram Generation

Guide to creating diagrams as code using PlantUML, Mermaid, and other tools.

## Mermaid (GitHub-native)

```markdown
```mermaid
graph LR
  A[User] --> B[API]
  B --> C[Database]
```

```mermaid
sequenceDiagram
  User->>API: Request
  API->>DB: Query
  DB-->>API: Result
  API-->>User: Response
```
```

## PlantUML

```plantuml
@startuml
User -> API: Request
API -> Database: Query
Database -> API: Result
API -> User: Response
@enduml
```

## Best Practices

✅ Version control diagrams as code
✅ Use consistent notation
✅ Keep diagrams simple
✅ Auto-generate images in CI

---

**Related Resources:**
- technical-writing-guide.md - When to use diagrams
