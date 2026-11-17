# Contributing to Pramaan

Thank you for your interest in contributing to **Pramaan**! We welcome improvements to our documentation, check catalogs, and prototype tooling.

---

## Code of Conduct

By participating in this project, you agree to abide by our [Code of Conduct](CODE_OF_CONDUCT.md).

---

## Ground Rules & Security Constraints

1. **Strictly Authorized Targets Only:** Never submit pull requests, test cases, or fixtures that point to or test real-world, production, or unauthorized targets (such as `worldmonitor.app` or public APIs). All test cases must use synthetic data or local mock services.
2. **Honesty First:** We never claim unbuilt capabilities. If you propose an enhancement or new check, clearly mark whether it is implemented or a design specification.
3. **No Secrets or Credentials:** Never commit credentials, API tokens, session cookies, or private data.

---

## Contribution Workflow

### 1. Fork & Branch
- Fork the repository and create a branch from `main`.
- Use descriptive branch names with a prefix:
  - `feat/feature-name` for new capabilities
  - `fix/bug-fix` for bug fixes
  - `docs/doc-update` for documentation improvements
  - `chore/maintenance` for build/CI maintenance

### 2. Commit Guidelines
We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:
```bash
feat: add check WM-INP-004 for proxy header injection
fix: resolve mobile navigation overflow on small screens
docs: update assessment methodology formulas
chore: bump github actions versions
```

### 3. Verification
Before submitting a pull request, verify that:
- JavaScript files pass syntax checks: `node --check app.js data.js`
- Local web server runs cleanly: `python -m http.server 8080`
- Markdown links in documentation are valid and functional.

### 4. Pull Requests
- Open a pull request against `main` using our pull request template.
- Describe the changes clearly and link any related issues.
