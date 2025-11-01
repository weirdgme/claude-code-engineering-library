# Technical Writing Guide

Guide to writing clear, effective technical documentation for developers.

## Writing Principles

### 1. Clarity First
- Use simple, direct language
- Avoid jargon (or explain it)
- One idea per sentence
- Short paragraphs (3-5 sentences)

### 2. Show Examples
Always include code examples:

```markdown
❌ Bad: "Configure the database connection"

✅ Good: "Configure the database connection in `.env`:
```
DATABASE_URL=postgresql://localhost:5432/mydb
```
```

### 3. Structure Content
```
1. What (brief description)
2. Why (use case/benefit)
3. How (step-by-step)
4. Example (working code)
```

## Document Structure

### README Template
```markdown
# Project Name
Brief 1-2 sentence description

## Features
- Bullet points

## Quick Start
```bash
# Minimal working example
npm install
npm run dev
```

## Documentation Links
## Installation
## Usage
## Configuration
## Contributing
```

### API Endpoint Documentation
```markdown
## POST /api/users

Creates a new user account.

### Request Body
```json
{
  "email": "user@example.com",
  "password": "securepass123"
}
```

### Response (201 Created)
```json
{
  "id": "usr_123",
  "email": "user@example.com",
  "created_at": "2024-01-15T10:30:00Z"
}
```

### Errors
- `400 Bad Request` - Invalid email format
- `409 Conflict` - Email already exists
```

## Style Guidelines

### Voice
- Active voice: "The API returns..." not "Data is returned by..."
- Present tense: "The function calculates..." not "The function will calculate..."
- Second person: "You can configure..." not "Users can configure..."

### Code Examples
```typescript
// ✅ Good: Full working example
import express from 'express';

const app = express();
app.get('/users', (req, res) => {
  res.json({ users: [] });
});

// ❌ Bad: Incomplete snippet
app.get('/users', ...
```

### Lists
Use numbered lists for sequential steps:
```markdown
1. Install dependencies
2. Configure environment
3. Run the application
```

Use bullet points for non-sequential items:
```markdown
- Feature A
- Feature B
- Feature C
```

## Common Patterns

### How-To Guide
```markdown
# How to Deploy to Production

## Prerequisites
- Node.js 18+
- AWS CLI configured
- Production environment variables

## Steps

1. Build the application:
```bash
npm run build
```

2. Run tests:
```bash
npm test
```

3. Deploy to AWS:
```bash
aws deploy push --application app-name
```

## Verification
Check deployment status:
```bash
aws deploy get-deployment --deployment-id <id>
```
```

### Troubleshooting Section
```markdown
## Troubleshooting

### Error: "Connection refused"
**Cause**: Database is not running
**Solution**: Start the database:
```bash
docker-compose up -d postgres
```

### Error: "Port 3000 already in use"
**Cause**: Another process is using port 3000
**Solution**: Kill the process or use a different port:
```bash
kill $(lsof -t -i:3000)
# or
PORT=3001 npm run dev
```
```

## Best Practices

✅ Start with a quick start guide (< 5 minutes to working app)
✅ Include code examples for every concept
✅ Use screenshots for UI-heavy documentation
✅ Keep examples up-to-date
✅ Test all code examples before publishing
✅ Use consistent formatting
✅ Link to related documentation

## Anti-Patterns

❌ Walls of text without code examples
❌ Outdated screenshots
❌ Broken links
❌ Assuming too much knowledge
❌ Incomplete code snippets
❌ No troubleshooting section
❌ Missing prerequisites

---

**Related Resources:**
- markdown-best-practices.md - Markdown formatting
- api-documentation.md - API-specific writing
- readme-engineering.md - README best practices
