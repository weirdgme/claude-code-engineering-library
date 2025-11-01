# Changelog Management

Guide to maintaining changelogs following Keep a Changelog format.

## Keep a Changelog Format

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- New feature X

### Changed
- Updated feature Y

### Fixed
- Bug fix Z

## [1.2.0] - 2024-01-15

### Added
- API endpoint for user management
- JWT authentication

### Changed
- Improved error handling

### Deprecated
- Old authentication method (use JWT)

### Removed
- Support for Node.js 14

### Fixed
- Memory leak in worker process

### Security
- Updated dependencies to fix CVE-2024-0001

## [1.1.0] - 2023-12-01
...

## [1.0.0] - 2023-11-01
- Initial release
```

## Semantic Versioning

```
MAJOR.MINOR.PATCH

MAJOR: Breaking changes
MINOR: New features (backward-compatible)
PATCH: Bug fixes (backward-compatible)

Examples:
1.0.0 → 1.0.1: Bug fix
1.0.1 → 1.1.0: New feature
1.1.0 → 2.0.0: Breaking change
```

## Automation

```bash
# Generate changelog from Git commits
npx conventional-changelog-cli -p angular -i CHANGELOG.md -s
```

---

**Related Resources:**
- docs-as-code-workflow.md - Versioning workflow
