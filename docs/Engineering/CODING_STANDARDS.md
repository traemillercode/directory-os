# DirectoryOS Coding Standards

## Purpose

This document defines the coding conventions engineers and AI coding assistants must follow when contributing to DirectoryOS. It formalizes and extends the conventions already established in [CURSOR_RULES.md](../AI/CURSOR_RULES.md), [CLAUDE_SYSTEM_PROMPT.md](../AI/CLAUDE_SYSTEM_PROMPT.md), and [GEMINI_GUIDE.md](../AI/GEMINI_GUIDE.md), and applies to all code regardless of who or what authored it.

These standards describe **what is expected**, not how to implement it — see the AI guides for illustrative examples.

## Language & Type Safety

- TypeScript is used across the codebase; strict typing is required.
- Explicit interfaces/types are required for function parameters, return values, and component props.
- The `any` type is disallowed except in narrowly justified, reviewed cases.
- Implicit return types on exported functions are disallowed — return types must be explicit or unambiguously inferable.

## Project Structure

Code is organized by responsibility, following the structure defined in CURSOR_RULES.md:

- `app/` — Next.js App Router routes, grouped by layout (`(auth)`, `(dashboard)`) and API routes
- `components/` — React components, subdivided into `ui/`, `forms/`, `dialogs/`, `layout/`
- `lib/` — utilities, API client, auth helpers, database helpers, validators
- `hooks/` — reusable React hooks
- `types/` — shared TypeScript types
- `constants/` — environment, routes, and configuration constants

New code must be placed in the directory matching its responsibility rather than introducing ad hoc structures. Proposed structural changes should be discussed and reflected back into this document.

## Naming Conventions

- **Components**: PascalCase (e.g., `ListingCard`, `ImportListingsDialog`)
- **Functions/variables**: camelCase (e.g., `fetchListings`, `validateEmail`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `MAX_LISTINGS`, `API_BASE_URL`)
- **Booleans**: `is`/`has` prefixes (e.g., `isLoading`, `hasError`)
- **API routes**: lowercase with hyphens (e.g., `/api/listings/:id/ai/generate-description`)
- **Database columns**: snake_case (e.g., `organization_id`, `created_at`)

## React & Component Conventions

- Use functional components with hooks; class components are disallowed unless there is a specific, documented need (e.g., an error boundary requirement).
- Type all component props via an explicit interface.
- Encapsulate reusable stateful logic in custom hooks rather than duplicating it across components.
- Use a server-state library (e.g., React Query) for fetched data rather than storing server responses directly in local component state.

## API Route Conventions

- Implement explicit handlers per HTTP method (`GET`, `POST`, `PATCH`, `DELETE`) rather than a single catch-all handler.
- Every route must: validate authentication, validate/parse input (via Zod schemas), perform the operation, and return a consistent response shape.
- All errors must go through a shared error-handling path that returns the standard error envelope defined in [API.md](../Architecture/API.md):

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request",
    "details": [{ "field": "email", "message": "Invalid email format" }]
  }
}
```

- Use the status codes defined in API.md consistently (200, 201, 204, 400, 401, 403, 404, 409, 422, 429, 500, 503).
- API changes must be backward compatible by default: add fields rather than remove/rename them, and follow the deprecation and feature-flag practices defined in ARCHITECTURE.md's API Versioning Strategy.

## Input Validation

- All external input (API requests, form submissions, imports) must be validated with Zod schemas before use.
- Validation schemas live alongside other shared validators (`lib/validators.ts` or equivalent) so they can be reused between client and server.
- Reject invalid input early with descriptive, field-level error details.

## Database Access

- All database access goes through Prisma; raw SQL is avoided except where Prisma cannot express the required query, and any such exception must be reviewed for SQL injection safety.
- Queries must select only the fields required for the use case rather than fetching entire rows by default.
- Related data must be fetched via Prisma `include`/relations to avoid N+1 query patterns.
- All list endpoints must paginate results; unbounded queries are not permitted.
- Every table remains scoped by `organization_id` and protected by Row-Level Security, per [DATABASE.md](../Architecture/DATABASE.md); new tables must not bypass tenant isolation.
- Primary keys are UUIDs, deletes are soft deletes (`deleted_at`), and `created_at`/`updated_at` timestamps are required on all tables, consistent with existing schema conventions.

## Error Handling

- All asynchronous operations that can fail must be wrapped in explicit error handling; silent failures (empty `catch` blocks) are disallowed.
- Use typed/custom error classes (e.g., `ValidationError`, `AuthenticationError`) to distinguish error handling paths.
- User-facing errors must produce an actionable message; unexpected errors must still be logged/reported (e.g., to Sentry) even when a generic message is shown to the user.

## Security Standards

- Never commit secrets, API keys, or credentials; all sensitive configuration is provided via environment variables (Vercel env vars, local `.env.local`).
- Enforce authentication and authorization checks on every non-public route.
- Apply rate limiting to public endpoints as defined in API.md.
- Sanitize and validate all user-supplied content to prevent injection and XSS; rely on Prisma parameterization and Content Security Policy rather than manual escaping.
- Include CSRF protection on state-changing operations.

## Performance Standards

- Avoid blocking the main thread with heavy client-side computation; move expensive work to API routes or background jobs.
- Lazy-load heavy or rarely used UI (e.g., dialogs, charts) using dynamic imports.
- Use the framework's built-in image optimization for all user-facing images.
- New code must not regress the performance targets defined in ARCHITECTURE.md (page load < 1s, API p99 < 100ms, DB query p99 < 50ms).

## Accessibility Standards

- Use semantic HTML elements and appropriate heading structure.
- Provide ARIA labels for icon-only interactive elements.
- Ensure all interactive elements are reachable and operable via keyboard.
- Maintain sufficient color contrast and support for dark mode, consistent with the Tailwind/shadcn design system.

## Documentation in Code

- Exported functions, especially those with non-obvious behavior (e.g., AI integrations, billing logic), must include doc comments describing parameters, return values, and usage examples.
- Code should be self-documenting through clear naming; comments explain *why*, not *what*, when the "what" is not already obvious from the code.

## Environment Variables

- All configuration values that differ by environment or are sensitive must be sourced from environment variables, never hardcoded.
- Required variables must be documented (name and purpose) without exposing actual secret values.
- `.env.local` and other files containing real secrets must remain gitignored.

## Enforcement

- Standards in this document are enforced via linting, type checking, and code review, as described in [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md).
- Reviewers should block merges for violations of the standards above; style nitpicks not covered by automated tooling should be raised as suggestions, not blockers.

## Related Documentation

- [DEVELOPMENT_GUIDE.md](./DEVELOPMENT_GUIDE.md) — environment setup and day-to-day workflow
- [TESTING_STRATEGY.md](./TESTING_STRATEGY.md) — how these standards are verified through tests
- [GITHUB_WORKFLOW.md](./GITHUB_WORKFLOW.md) — how changes following these standards move through review and release
- [ARCHITECTURE.md](../Architecture/ARCHITECTURE.md), [API.md](../Architecture/API.md), [DATABASE.md](../Architecture/DATABASE.md)
