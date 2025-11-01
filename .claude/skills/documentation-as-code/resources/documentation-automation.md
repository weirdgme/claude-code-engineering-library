# Documentation Automation

Guide to automating documentation workflows with linting, link checking, and CI/CD.

## Link Checking

```yaml
# .github/workflows/link-check.yml
name: Check Links
on: [pull_request]
jobs:
  link-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: gaurav-nelson/github-action-markdown-link-check@v1
        with:
          config-file: '.markdown-link-check.json'
```

```json
// .markdown-link-check.json
{
  "ignorePatterns": [
    { "pattern": "^http://localhost" }
  ],
  "timeout": "20s",
  "retryOn429": true
}
```

## Markdown Linting

```yaml
# .markdownlint.json
{
  "default": true,
  "MD013": false,
  "MD033": false
}
```

```bash
npx markdownlint-cli '**/*.md'
```

## Spell Checking

```bash
npx cspell "docs/**/*.md"
```

## Auto-Generate Docs

```typescript
// Generate API docs from code
import { generateDocs } from 'typedoc';

generateDocs({
  entryPoints: ['./src/index.ts'],
  out: './docs/api'
});
```

---

**Related Resources:**
- docs-as-code-workflow.md - Complete workflow
