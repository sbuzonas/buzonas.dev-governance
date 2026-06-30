# ADR-005: GitHub Actions for CI/CD

**Date:** 2026-06-29
**Status:** Accepted

## Context

The project requires automated build, test, and deployment pipelines. The source repository is on GitHub.

## Decision

Use **GitHub Actions** for all CI/CD workflows.

## Rationale

- Native GitHub integration — no external CI service required
- Free for public repositories
- Workflows are version-controlled in `.github/workflows/` alongside source code
- GITHUB_TOKEN provides authenticated deployment to GitHub Pages without secrets management
- Sufficient for the project's pipeline requirements: typecheck, lint, build, Lighthouse CI, deploy

## Consequences

- **Positive:** Zero additional tooling, version-controlled pipelines, no external service credentials
- **Negative:** GitHub Actions YAML syntax is verbose; debugging runner failures requires pushing commits
- **Workflow design:** Two workflows — `ci.yml` (runs on every push to `main` and PRs) and `cd.yml` (runs on semver tags and GitHub Release publishes)

## Alternatives Considered

- **CircleCI / Travis CI** — external services; no advantage over GitHub Actions for a GitHub-hosted project
- **Vercel deploy pipeline** — would replace GitHub Actions for deployment; deferred with hosting decision (ADR-002)

---
