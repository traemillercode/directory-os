# DirectoryOS GitHub Workflow

## Purpose

This document defines how engineers use Git and GitHub to collaborate on DirectoryOS: branching, commits, pull requests, code review, and how changes flow from a developer's machine into production. It is the authoritative process reference alongside the automation details in [AUTOMATION.md](../Automation/AUTOMATION.md) and the release strategy in [03_ROADMAP.md](../Product/03_ROADMAP.md).

## Branching Strategy

DirectoryOS follows the branching model defined in 03_ROADMAP.md:

| Branch | Purpose |
|---|---|
| `main` | Always production-ready. Deploys to production. |
| `develop` | Integration branch containing the latest merged features. Deploys to staging automatically. |
| `feature/*` | Individual features or improvements, branched from `develop`. |
| `release/*` | Stabilization branches cut from `develop` ahead of a release. |
| `hotfix/*` | Emergency patches branched from `main` for urgent production fixes. |

### Branch Naming

Branch names are descriptive and prefixed by type, per [CURSOR_RULES.md](../AI/CURSOR_RULES.md):

```
feature/add-bulk-import
fix/listings-pagination-bug
docs/api-documentation
refactor/database-queries
hotfix/stripe-webhook-failure
```

## Commit Messages

DirectoryOS uses **Conventional Commits**, as defined in [CURSOR_RULES.md](../AI/CURSOR_RULES.md) and [AUTOMATION.md](../Automation/AUTOMATION.md):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `ci`

**Example**:

```
feat(listings): add bulk import from CSV

Implement CSV file upload with:
- Validation of listing data
- Duplicate detection
- Progress tracking
- Error reporting

Closes #123
```

Commit message format is enforced via pre-commit hooks (Husky + lint-staged) and, where configured, Commitizen tooling, as described in [AUTOMATION.md](../Automation/AUTOMATION.md).

## Pull Request Workflow

1. **Open early**: Open a pull request as soon as meaningful work exists, marking it as a draft if it is not yet ready for review. This enables continuous CI feedback and early input.
2. **Describe the change**: The PR description should explain the problem being solved, the approach taken, and any follow-up work, linking to relevant issues (e.g., `Closes #123`).
3. **Keep PRs scoped**: A pull request should represent one logical change. Unrelated fixes or refactors belong in separate PRs.
4. **Required checks**: Every PR must pass the automated pipeline defined in [AUTOMATION.md](../Automation/AUTOMATION.md) before it can be merged:
   - Lint & format check
   - TypeScript type check
   - Unit and integration tests (with coverage reporting)
   - Security scanning (CodeQL, dependency scanning)
5. **Required review**: At least one approving review is required before merge. Reviewers evaluate the change against [CODING_STANDARDS.md](./CODING_STANDARDS.md) and [TESTING_STRATEGY.md](./TESTING_STRATEGY.md).
6. **Resolve feedback**: All review comments must be resolved (addressed or explicitly discussed) before merge.
7. **Merge**: Once approved and green, the PR is merged into its target branch (`develop` for features, `main` for hotfixes/releases) using the repository's standard merge method.

## Code Review Expectations

- Reviewers focus on correctness, security, adherence to CODING_STANDARDS.md, test coverage, and architectural fit — not personal style preferences already enforced by linting/formatting tools.
- Authors should respond to every review comment, either with a code change or a clear explanation.
- Large or high-risk PRs (auth, billing, database schema, multi-tenancy) should have a second reviewer when feasible.
- Automated dependency update PRs (Dependabot/Renovate) still require passing tests before merge; minor/patch updates may be configured for auto-merge per [AUTOMATION.md](../Automation/AUTOMATION.md), but major version bumps require manual review.

## Automated Checks & Bots

As defined in [AUTOMATION.md](../Automation/AUTOMATION.md):

- **Pre-commit hooks** (Husky + lint-staged): run ESLint, Prettier, and TypeScript checks, and validate commit message format before a commit is created.
- **Dependabot / Renovate**: open automated dependency update PRs; minor/patch updates may auto-merge after tests pass, while major updates require manual review.
- **CodeQL**: scans JavaScript/TypeScript and SQL for security vulnerabilities on every push and PR.
- **CI test pipeline**: runs unit and integration tests with coverage reporting on every push and PR.

## Deployment & Release Flow

Per [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md)'s CI/CD Pipeline and [03_ROADMAP.md](../Product/03_ROADMAP.md)'s Release Strategy:

1. Developer pushes to a `feature/*` branch and opens a PR against `develop`.
2. GitHub Actions runs linting, type checking, unit tests, and integration tests.
3. PR requires review approval and passing checks before merge.
4. Merging to `develop` triggers an automatic deployment to **staging**.
5. QA (automated and manual) is performed on staging.
6. Promotion to **production** requires manual approval, as defined in [AUTOMATION.md](../Automation/AUTOMATION.md)'s deployment workflow.
7. Rollback is available via the Vercel dashboard if a production issue is detected.

### Release Cadence by Phase

| Phase | Duration | Pattern | Deployment |
|---|---|---|---|
| MVP | 12 weeks | Continuous (daily) | Vercel staging → production |
| Phase 2 | 12 weeks | Bi-weekly | Feature flags for safe rollout |
| Phase 3 | 28 weeks | Monthly | Canary deployment (5% first) |

### Hotfixes

Emergency production fixes branch from `main` as `hotfix/*`, follow the same review and CI requirements as a normal PR (checks are not skipped for urgency), and are merged back into both `main` and `develop` to keep branches in sync.

## Issue & Work Tracking

- Work is tracked via GitHub Issues, referenced in commit messages and PR descriptions (e.g., `Closes #123`).
- PRs should link the issue(s) they resolve so that merging automatically closes tracked work.

## Related Documentation

- [DEVELOPMENT_GUIDE.md](./DEVELOPMENT_GUIDE.md) — local environment setup and daily workflow
- [CODING_STANDARDS.md](./CODING_STANDARDS.md) — standards enforced during review
- [TESTING_STRATEGY.md](./TESTING_STRATEGY.md) — testing requirements enforced by CI
- [AUTOMATION.md](../Automation/AUTOMATION.md) — full CI/CD and automation implementation details
- [03_ROADMAP.md](../Product/03_ROADMAP.md) — release cadence and branching strategy by phase
