# Documentation Sites

Guide to setting up documentation sites using Docusaurus, MkDocs, GitBook, and other static site generators.

## Docusaurus (React-based)

```bash
npx create-docusaurus@latest my-docs classic
cd my-docs
npm start
```

```javascript
// docusaurus.config.js
module.exports = {
  title: 'My Docs',
  tagline: 'Documentation site',
  url: 'https://docs.example.com',
  baseUrl: '/',
  organizationName: 'myorg',
  projectName: 'my-docs',
  themeConfig: {
    navbar: {
      title: 'Docs',
      items: [
        { to: 'docs/intro', label: 'Tutorial', position: 'left' },
        { href: 'https://github.com/myorg/my-docs', label: 'GitHub', position: 'right' },
      ],
    },
  },
};
```

## MkDocs (Python-based)

```bash
pip install mkdocs
mkdocs new my-docs
cd my-docs
mkdocs serve
```

```yaml
# mkdocs.yml
site_name: My Docs
theme:
  name: material
nav:
  - Home: index.md
  - Getting Started: getting-started.md
  - API Reference: api.md
```

## Deployment

```yaml
# .github/workflows/docs.yml
name: Deploy Docs
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build
        run: npm run build
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build
```

---

**Related Resources:**
- documentation-automation.md - CI/CD for docs
