# DirectoryOS Development Guide

## Purpose

This guide defines how engineers set up, work in, and operate the DirectoryOS development environment day to day. It complements the [Architecture](../Architecture/ARCHITECTURE.md), [API Specification](../Architecture/API.md), and [Database Design](../Architecture/DATABASE.md) documentation, which remain the source of truth for system design decisions.

## Technology Foundation

Per [01_PROJECT.md](../Product/01_PROJECT.md) and [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md):

- **Frontend**: Next.js 14+ (App Router), TypeScript, Tailwind CSS, shadcn/ui
- **Backend**: Supabase (PostgreSQL, Auth, Realtime, Storage), Prisma ORM, Vercel Serverless Functions
- **Integrations**: Stripe, Resend, Mapbox, Claude (Anthropic), Gemini (Google), n8n
- **Observability**: Sentry, Google Analytics 4, Microsoft Clarity, Vercel Analytics
- **Developer Tools**: Cursor, GitHub Copilot, GitHub Actions, Git & GitHub

## Environments

DirectoryOS maintains three environments, as defined in ARCHITECTURE.md:

| Environment | Purpose | Data | Notes |
|---|---|---|---|
| **Development** | Local engineering work | Local database or Supabase dev branch | External integrations mocked, loose rate limiting |
| **Staging** | Pre-release validation | Weekly refreshed copy of production | Mirrors production infrastructure, no alerting |
| **Production** | Live customer traffic | Live user data | Full monitoring, alerting, automated backups |

## Local Development Setup

1. **Clone the repository** and check out the appropriate feature branch (see [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md)).
2. **Install dependencies** using the project's package manager lockfile — never install packages ad hoc without updating the lockfile.
3. **Configure environment variables** in a local `.env.local` file (gitignored). Required variables include Supabase URL/keys, Stripe keys, AI provider keys, and analytics IDs, as documented in [CURSOR_RULES.md](../AI/CURSOR_RULES.md). Never commit secrets to source control.
4. **Provision a local or dev-branch database** via Supabase, and apply the current Prisma schema/migrations.
5. **Run the application locally** and confirm the dashboard, authentication, and API routes respond as expected before starting new work.

## Day-to-Day Development Workflow

1. Pick up a scoped unit of work (feature, fix, or chore) from the roadmap or issue tracker.
2. Create a branch following the naming conventions in [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md).
3. Implement the change following [CODING_STANDARDS.md](./CODING_STANDARDS.md).
4. Write or update tests per [TESTING_STRATEGY.md](./TESTING_STRATEGY.md).
5. Run linting, type checking, and tests locally before pushing.
6. Open a pull request early (draft if work is in progress) to enable continuous feedback.
7. Address review feedback and required checks until the PR is approved and CI is green.
8. Merge according to the branching strategy; staging deployment follows automatically.

## Database Change Workflow

- All schema changes go through Prisma migrations — never modify the database schema directly in an environment.
- Migrations are reviewed as part of the pull request, alongside the corresponding schema and query changes.
- Multi-tenancy (`organization_id` scoping) and Row-Level Security (RLS) policies described in [DATABASE.md](../Architecture/DATABASE.md) must be preserved for any new table or column.
- Migrations are applied automatically to staging on merge, and to production during the deployment step described in [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md).

## Working with AI Coding Assistants

DirectoryOS is built with an AI-first engineering culture. When using Cursor, GitHub Copilot, Claude, or Gemini to assist with development:

- Follow the conventions in [CURSOR_RULES.md](../AI/CURSOR_RULES.md), [CLAUDE_SYSTEM_PROMPT.md](../AI/CLAUDE_SYSTEM_PROMPT.md), and [GEMINI_GUIDE.md](../AI/GEMINI_GUIDE.md).
- Treat AI-generated code as a draft: it must still satisfy [CODING_STANDARDS.md](./CODING_STANDARDS.md) and pass the same review and testing bar as hand-written code.
- Never paste secrets, customer data, or PII into AI tools or prompts.
- Keep code self-documenting and well-typed so AI tools (and human reviewers) can reason about it accurately.

## Performance & Reliability Expectations

Engineers are expected to design and validate changes against the performance targets in [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md):

- Page load (LCP) < 1s
- API response time < 100ms (p99)
- Database query time < 50ms (p99)
- Support for 1,000+ concurrent users

Any change with a material performance impact should include before/after measurements in the pull request description.

## Security & Compliance Expectations

- Follow the security measures defined in [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md): JWT auth, input validation via Zod, Prisma-based SQL injection prevention, CSP-based XSS protection, and CSRF protection on state-changing operations.
- Never commit secrets; use environment variables managed via Vercel and local `.env.local` files.
- Treat all PII in accordance with GDPR/CCPA requirements referenced in [01_PROJECT.md](../Product/01_PROJECT.md) and [02_MVP.md](../Product/02_MVP.md).
- Report suspected security issues immediately rather than including them in a routine pull request.

## Getting Unstuck

Per [CURSOR_RULES.md](../AI/CURSOR_RULES.md):

1. **Unclear error?** Read the stack trace bottom-to-top.
2. **Type error?** Run the project's type-check script for full context.
3. **Build failing?** Clear local build caches and rebuild.
4. **Database issue?** Check the Supabase dashboard for connection/service status.
5. **API not working?** Inspect the Network tab and verify the auth token.

If still stuck, escalate to the team rather than shipping a workaround that violates the standards in this documentation set.

## Related Documentation

- [CODING_STANDARDS.md](./CODING_STANDARDS.md) — code style, structure, and conventions
- [TESTING_STRATEGY.md](./TESTING_STRATEGY.md) — testing levels, tools, and coverage expectations
- [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md) — branching, commits, reviews, and CI/CD
- [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md), [API.md](../Architecture/API.md), [DATABASE.md](../Architecture/DATABASE.md)
- [AUTOMATION.md](../Automation/AUTOMATION.md) — automation philosophy and tooling
