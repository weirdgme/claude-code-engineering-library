# Markdown Best Practices

Guide to effective Markdown usage following GitHub Flavored Markdown (GFM) standards.

## Headings

```markdown
# H1 - Document Title (one per document)
## H2 - Major sections
### H3 - Subsections
#### H4 - Sub-subsections

# ✅ Good: Clear hierarchy
# API Documentation
## Authentication
### JWT Tokens
#### Token Expiration

# ❌ Bad: Skipping levels
# API Documentation
#### Token Expiration
```

## Code Blocks

```markdown
# Inline code
Use `backticks` for inline code.

# Code blocks with syntax highlighting
```typescript
const user = { name: "John" };
```

# Shell commands
```bash
npm install
npm run dev
```
```

## Links

```markdown
# Inline links
[Link text](https://example.com)

# Reference links (better for multiple uses)
[Google][1]
[GitHub][2]

[1]: https://google.com
[2]: https://github.com

# Internal links
[See Architecture](./architecture.md)
[Jump to section](#installation)
```

## Images

```markdown
# Basic image
![Alt text](./images/diagram.png)

# Image with title
![Architecture Diagram](./arch.png "System Architecture")

# Linked image
[![Badge](badge.png)](https://example.com)
```

## Tables

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Value 1  | Value 2  | Value 3  |
| Value 4  | Value 5  | Value 6  |

# Alignment
| Left | Center | Right |
|:-----|:------:|------:|
| L1   |   C1   |    R1 |
```

## Lists

```markdown
# Unordered
- Item 1
- Item 2
  - Nested item
  - Another nested

# Ordered
1. First step
2. Second step
   1. Sub-step A
   2. Sub-step B

# Task lists
- [x] Completed task
- [ ] Incomplete task
```

## Emphasis

```markdown
*Italic* or _italic_
**Bold** or __bold__
***Bold and italic***
~~Strikethrough~~
`Code`
```

## Blockquotes

```markdown
> Single line quote

> Multi-line quote
> continues here
> and here

> **Note:** Use for important callouts
```

## Horizontal Rules

```markdown
---
or
***
or
___
```

## Best Practices

✅ Use consistent heading hierarchy
✅ Add blank lines around headings, lists, code blocks
✅ Use fenced code blocks with language tags
✅ Provide alt text for images
✅ Use relative links for internal docs
✅ Use reference links for repeated URLs
✅ Keep lines under 120 characters (where possible)

## Anti-Patterns

❌ Mixing ordered/unordered list styles
❌ Skipping heading levels (H1 → H4)
❌ No blank lines between elements
❌ Missing language tags on code blocks
❌ Broken relative links
❌ No alt text on images

---

**Related Resources:**
- technical-writing-guide.md - Writing style
- documentation-automation.md - Linting and validation
