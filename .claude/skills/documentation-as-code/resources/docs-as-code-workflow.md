# Docs-as-Code Workflow

Complete workflow for treating documentation like code with Git, reviews, and CI/CD.

## Git Workflow

```bash
# Create documentation branch
git checkout -b docs/add-api-guide

# Write documentation
vim docs/api-guide.md

# Commit with clear message
git add docs/api-guide.md
git commit -m "docs: Add API authentication guide"

# Push and create PR
git push origin docs/add-api-guide
gh pr create --title "Add API authentication guide"
```

## Pull Request Review

```markdown
## Documentation PR Checklist

- [ ] Spelling and grammar checked
- [ ] Code examples tested
- [ ] Links verified (no broken links)
- [ ] Screenshots up-to-date (if any)
- [ ] TOC updated
- [ ] Related docs updated
```

## CI/CD Pipeline

```yaml
# .github/workflows/docs.yml
name: Documentation
on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Lint Markdown
        run: npx markdownlint-cli '**/*.md'

  link-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Check Links
        uses: gaurav-nelson/github-action-markdown-link-check@v1

  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build Docs Site
        run: |
          npm install
          npm run build
      - name: Deploy to GitHub Pages
        if: github.ref == 'refs/heads/main'
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build
```

## Versioning

```
docs/
  v1.0/
    getting-started.md
    api-reference.md
  v2.0/
    getting-started.md
    api-reference.md
  latest/ → symlink to v2.0
```

## Best Practices

✅ Update docs in same PR as code changes
✅ Require docs review before merge
✅ Auto-deploy docs on main branch
✅ Version docs alongside code
✅ Use branch protection for docs/

---

**Related Resources:**
- documentation-automation.md - Automation tools
- changelog-management.md - Version tracking
