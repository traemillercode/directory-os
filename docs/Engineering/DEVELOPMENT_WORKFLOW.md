# DirectoryOS Development Workflow

## Purpose

This document is the official, authoritative definition of the DirectoryOS development process — how work moves from an idea to production. It consolidates the branch strategy, commit conventions, pull request process, code review rules, testing requirements, documentation obligations, AI tool participation, and Definition of Done into a single reference.

Where more implementation detail is needed, this document links to the existing engineering, architecture, and AI documentation that remains the source of truth for those specifics: [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md), [DEVELOPMENT_GUIDE.md](./DEVELOPMENT_GUIDE.md), [CODING_STANDARDS.md](./CODING_STANDARDS.md), [TESTING_STRATEGY.md](./TESTING_STRATEGY.md), [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md), [API.md](../Architecture/API.md), [DATABASE.md](../Architecture/DATABASE.md), [AUTOMATION.md](../Automation/AUTOMATION.md), and the AI guides in [docs/AI](../AI/).

## Branch Strategy

DirectoryOS follows the branching model defined in [03_ROADMAP.md](../Product/03_ROADMAP.md) and [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md):

| Branch | Purpose |
|---|---|
| `main` | Always production-ready. Deploys to production. |
| `develop` | Integration branch with the latest merged work. Deploys to staging automatically. |
| `feature/*` | Individual features or improvements, branched from `develop`. |
| `release/*` | Stabilization branches cut from `develop` ahead of a release. |
| `hotfix/*` | Emergency patches branched from `main` for urgent production fixes, merged back into both `main` and `develop`. |

Branch names are descriptive and prefixed by type (e.g., `feature/add-bulk-import`, `fix/listings-pagination-bug`, `docs/api-documentation`, `refactor/database-queries`, `hotfix/stripe-webhook-failure`), per [CURSOR_RULES.md](../AI/CURSOR_RULES.md).

## Commit Conventions

DirectoryOS uses **Conventional Commits**:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `ci`

Commits should be scoped to a single logical change, reference related issues in the footer (e.g., `Closes #123`), and pass the commit message format enforced by pre-commit hooks (Husky + lint-staged and, where configured, Commitizen), as described in [AUTOMATION.md](../Automation/AUTOMATION.md).

## Pull Request Process

1. **Open early**: Open a PR as soon as meaningful work exists, marked as a draft if not yet ready for review, to enable continuous CI feedback.
2. **Describe the change**: Explain the problem, the approach, and any follow-up work, linking related issues (e.g., `Closes #123`).
3. **Keep PRs scoped**: One logical change per PR; unrelated fixes or refactors belong in separate PRs.
4. **Pass required checks**: Every PR must pass the automated pipeline in [AUTOMATION.md](../Automation/AUTOMATION.md) — lint & format, TypeScript type check, unit/integration tests with coverage, and security scanning (CodeQL, dependency scanning).
5. **Get required review**: At least one approving review is required before merge.
6. **Resolve feedback**: All review comments must be addressed or explicitly discussed before merge.
7. **Merge**: Merge into the correct target branch (`develop` for features, `main` for hotfixes/releases) once approved and green.

Full detail on required checks, deployment flow, and release cadence lives in [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md).

## Code Review Rules

- Reviewers evaluate correctness, security, adherence to [CODING_STANDARDS.md](./CODING_STANDARDS.md), test coverage per [TESTING_STRATEGY.md](./TESTING_STRATEGY.md), and architectural fit with [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md) — not personal style preferences already enforced by linting/formatting.
- Authors must respond to every review comment, either with a code change or a clear explanation.
- High-risk PRs (authentication, billing, database schema, multi-tenancy) require a second reviewer when feasible.
- Reviewers block merges for standards violations; style nitpicks not covered by automated tooling are raised as suggestions, not blockers.
- Automated dependency update PRs still require passing tests; minor/patch updates may auto-merge per [AUTOMATION.md](../Automation/AUTOMATION.md), but major version bumps require manual review.

## Testing Requirements

Per [TESTING_STRATEGY.md](./TESTING_STRATEGY.md), new functionality is not complete until it is covered by appropriate automated tests:

- **Unit tests** for validators, utilities, and business logic.
- **Integration tests** for API routes, covering auth, validation, and tenant-isolation (Row-Level Security) behavior.
- **End-to-end tests** for critical user journeys (signup/login, listing creation/publishing, lead capture, analytics), run at minimum before merges to `develop`/`main`.
- **Non-functional tests** (performance, security, accessibility) for high-risk or critical-path changes.
- Critical paths (authentication, listings, leads, billing) require **95%+ coverage** before production launch, tracked and reported in CI.
- Failing tests block merge; flaky tests are treated as bugs to be fixed or quarantined with a tracked follow-up, never silently disabled.

## When Documentation Must Be Updated

Documentation is part of the deliverable, not an afterthought. A PR must update the relevant documentation whenever it:

- Changes system design, data flow, or infrastructure — update [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md).
- Adds, removes, or changes an API endpoint, request/response shape, or error code — update [API.md](../Architecture/API.md).
- Adds or modifies a database table, column, relationship, or RLS policy — update [DATABASE.md](../Architecture/DATABASE.md).
- Changes coding conventions, project structure, or naming rules — update [CODING_STANDARDS.md](./CODING_STANDARDS.md) (and the AI guides in [docs/AI](../AI/) if they reference the changed convention).
- Changes testing expectations, coverage targets, or test tooling — update [TESTING_STRATEGY.md](./TESTING_STRATEGY.md).
- Changes branching, commit, PR, CI/CD, or release processes — update [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md), [AUTOMATION.md](../Automation/AUTOMATION.md), or this document.
- Changes local setup or day-to-day developer workflow — update [DEVELOPMENT_GUIDE.md](./DEVELOPMENT_GUIDE.md).
- Adds or changes product scope, roadmap, or milestones — update the relevant file in [docs/Product](../Product/).

A PR that changes behavior described in existing documentation without updating that documentation is not considered ready for review, and reviewers should request the documentation update before approving.

## How AI Tools Should Participate

DirectoryOS is built with an AI-first engineering culture (Cursor, GitHub Copilot, Claude, Gemini). AI participation follows the same process and quality bar as human-authored work:

- Follow the conventions in [CURSOR_RULES.md](../AI/CURSOR_RULES.md), [CLAUDE_SYSTEM_PROMPT.md](../AI/CLAUDE_SYSTEM_PROMPT.md), and [GEMINI_GUIDE.md](../AI/GEMINI_GUIDE.md).
- Treat AI-generated code and text as a draft: it must satisfy [CODING_STANDARDS.md](./CODING_STANDARDS.md), pass the same CI checks, and go through the same human review before merge — AI output is never merged unreviewed.
- Human review remains mandatory for AI-assisted or AI-generated product features (e.g., AI-generated listing descriptions, SEO suggestions), per [AUTOMATION.md](../Automation/AUTOMATION.md); this is a human-in-the-loop requirement, not just a code-review requirement.
- Never paste secrets, customer data, or PII into AI tools or prompts.
- Keep code self-documenting and well-typed so AI tools and human reviewers can both reason about it accurately.
- Contributions authored primarily by an AI agent must still follow branch naming, commit conventions, and the PR process defined in this document — there is no separate process for AI-authored changes.

## Definition of Done

A unit of work (feature, fix, or chore) is Done only when all of the following are true:

- [ ] Code follows [CODING_STANDARDS.md](./CODING_STANDARDS.md) (typing, structure, naming, error handling, security).
- [ ] Automated tests exist per [TESTING_STRATEGY.md](./TESTING_STRATEGY.md) and pass locally and in CI.
- [ ] Linting, formatting, and TypeScript type checks pass.
- [ ] Security scanning (CodeQL, dependency scanning) passes with no new vulnerabilities.
- [ ] Relevant documentation is updated per the "When Documentation Must Be Updated" section above.
- [ ] The pull request has at least one approving review, with all review comments resolved.
- [ ] The change has been merged into its correct target branch and, where applicable, verified on staging before production promotion.
- [ ] Any related issue is linked and closed by the merge.

## Related Documentation

- [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md) — detailed branching, commit, PR, and release mechanics
- [DEVELOPMENT_GUIDE.md](./DEVELOPMENT_GUIDE.md) — local environment setup and day-to-day workflow
- [CODING_STANDARDS.md](./CODING_STANDARDS.md) — code conventions enforced during review
- [TESTING_STRATEGY.md](./TESTING_STRATEGY.md) — testing levels, tools, and coverage expectations
- [AUTOMATION.md](../Automation/AUTOMATION.md) — CI/CD pipeline and automation implementation details
- [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md), [API.md](../Architecture/API.md), [DATABASE.md](../Architecture/DATABASE.md) — system design source of truth
- [docs/AI](../AI/) — AI tool-specific guides (Cursor, Claude, Gemini)
