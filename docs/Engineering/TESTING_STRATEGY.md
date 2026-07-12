# DirectoryOS Testing Strategy

## Purpose

This document defines how DirectoryOS is tested, by whom, and to what standard. It operationalizes the quality bar implied by the MVP success criteria in [02_MVP.md](../Product/02_MVP.md) and [03_ROADMAP.md](../Product/03_ROADMAP.md), and works alongside the CI/CD automation described in [AUTOMATION.md](../Automation/AUTOMATION.md).

## Testing Philosophy

- **Tests are not optional.** New functionality is not considered complete until it is covered by appropriate automated tests.
- **Test the contract, not the implementation.** Tests should validate behavior (inputs/outputs, side effects) rather than internal implementation details, so refactors do not require rewriting tests.
- **Fail fast, fail loud.** Failing tests must block merges; there is no "known failing test" state in the main branch.
- **Human review remains required for AI-assisted or automated actions** (e.g., AI-generated descriptions, SEO suggestions) per [AUTOMATION.md](../Automation/AUTOMATION.md) — automated tests verify the underlying system behavior, not the judgment calls reserved for humans.

## Test Levels

### 1. Unit Tests

**Scope**: Individual functions, utilities, and validators in isolation (e.g., Zod schemas in `lib/validators.ts`, formatting helpers, business logic functions).

**Expectations**:
- Cover both valid and invalid input cases (e.g., a listing validator test suite must assert acceptance of valid listings and rejection of listings that violate length, format, or required-field rules).
- Run quickly and without external dependencies (no live database, no network calls); external services are mocked.
- Live alongside the code they test (e.g., `lib/__tests__/`) or in a parallel `tests/unit` structure.

### 2. Integration Tests

**Scope**: API routes and their interaction with the database, authentication, and validation layers.

**Expectations**:
- Exercise real request/response cycles against a test database instance (e.g., a disposable PostgreSQL instance provisioned in CI).
- Verify authentication/authorization enforcement, input validation error responses, and the standard error envelope defined in [API.md](../Architecture/API.md).
- Verify multi-tenant isolation: a request scoped to one organization must never read or mutate another organization's data (Row-Level Security enforcement from [DATABASE.md](../Architecture/DATABASE.md)).

### 3. End-to-End (E2E) Tests

**Scope**: Full user journeys through the UI, simulating real user behavior in a browser.

**Expectations**:
- Cover critical paths such as: sign-up/login, creating and publishing a listing, capturing a lead through a public listing page, and viewing analytics.
- Run against a staging-like environment or a fully seeded local/CI environment.
- Kept resilient to incidental UI changes by targeting semantic roles/text rather than brittle selectors.

### 4. Non-Functional Testing

- **Performance testing**: Validate the performance targets from [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md) (API p99 < 100ms, DB query p99 < 50ms, page load < 1s) for critical endpoints and pages, especially before major releases.
- **Security testing**: Automated dependency and static analysis scanning (CodeQL, dependency scanning) runs on every change per [AUTOMATION.md](../Automation/AUTOMATION.md); manual security review is required for changes touching authentication, authorization, payments, or PII handling.
- **Accessibility testing**: Critical user-facing flows are checked against basic accessibility requirements (semantic HTML, keyboard operability, ARIA labeling) defined in [CODING_STANDARDS.md](./CODING_STANDARDS.md).

## What Must Be Tested

- All Zod validators and business-logic utilities (unit tests).
- Every new or modified API route: happy path, validation failures, auth failures, and tenant-isolation behavior (integration tests).
- Every critical user journey listed under MVP success criteria in 02_MVP.md (E2E tests), including:
  - Listing creation, editing, and publishing
  - Bulk import/export
  - AI description generation (verifying the human-review step is enforced, not verifying AI output content)
  - Lead capture and lead status updates
  - Authentication (signup, login, logout, token refresh, password reset)
- Payment and billing flows (Stripe integration) using sandbox/test-mode credentials only — never live payment credentials.

## Test Data & Environments

- Tests must never run against production data or production credentials.
- Integration and E2E tests run against a dedicated test database (spun up in CI, e.g., via a Postgres service container) that is seeded and torn down per run.
- Secrets required for test runs (e.g., test-mode Stripe keys) are provided via CI secrets, never hardcoded in test files.
- External third-party services (Claude, Gemini, Mapbox, Resend) are mocked or stubbed in unit and integration tests; a smaller set of contract/smoke tests may run against sandbox credentials where the provider supports it.

## Coverage Expectations

- Per the MVP release checklist in 02_MVP.md, critical paths (authentication, listings, leads, billing) require **95%+ test coverage** before production launch.
- Coverage is tracked in CI (see AUTOMATION.md's testing pipeline) and reported on every pull request.
- Coverage targets apply to critical business logic and API routes; purely presentational UI code is held to a lower, but non-zero, bar — reviewers use judgment on what constitutes meaningful coverage versus testing for its own sake.

## Testing in the CI/CD Pipeline

As defined in [AUTOMATION.md](../Automation/AUTOMATION.md) and [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md), every push and pull request triggers:

1. Linting and formatting checks
2. TypeScript type checking
3. Unit test suite
4. Integration test suite (against a provisioned test database)
5. Coverage reporting
6. Security scanning (CodeQL, dependency scanning)

E2E tests run at minimum before merges to `develop`/`main` and before any production deployment, and may also run on-demand for high-risk pull requests.

A pull request cannot be merged unless all required checks pass. Flaky tests are treated as bugs to be fixed or quarantined with a tracked follow-up — they are not disabled silently.

## Manual QA

- Automated tests are the primary quality gate, but manual QA is performed on staging before production promotion for each release, per the CI/CD pipeline described in [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md).
- Manual QA focuses on exploratory testing, visual/UX review, and validating AI-generated content review flows that are inherently human-in-the-loop.
- Any bug found in manual QA that is not caught by automated tests should result in a new automated test covering that gap.

## Related Documentation

- [CODING_STANDARDS.md](./CODING_STANDARDS.md) — standards that tests verify
- [DEVELOPMENT_GUIDE.md](./DEVELOPMENT_GUIDE.md) — local setup for running tests
- [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md) — how test results gate merges and releases
- [AUTOMATION.md](../Automation/AUTOMATION.md) — CI/CD pipeline details
- [02_MVP.md](../Product/02_MVP.md) — MVP launch checklist and coverage targets
