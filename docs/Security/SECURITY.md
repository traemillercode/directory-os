# DirectoryOS Security

This document describes the security model for DirectoryOS: a multi-tenant SaaS platform built on Next.js, Supabase (PostgreSQL + Auth), and Vercel. It is the reference for how the system authenticates users, authorizes actions, isolates tenant data, manages secrets, protects data, logs activity, secures its APIs, and meets common compliance obligations.

## Table of Contents

- [Authentication](#authentication)
- [Authorization](#authorization)
- [Role-Based Access Control (RBAC)](#role-based-access-control-rbac)
- [Tenant Isolation](#tenant-isolation)
- [Secrets Management](#secrets-management)
- [Encryption](#encryption)
- [Audit Logging](#audit-logging)
- [API Security](#api-security)
- [OWASP Considerations](#owasp-considerations)
- [Compliance (GDPR/CCPA)](#compliance-gdprccpa)
- [Reporting a Vulnerability](#reporting-a-vulnerability)

## Authentication

DirectoryOS uses **Supabase Auth** as the identity provider for all users (customers, organization admins, and internal staff).

- **Credentials**: Email/password (bcrypt-hashed via Supabase) and supported OAuth providers (e.g., Google).
- **Tokens**: Short-lived JWT access tokens (15 minute expiry) plus long-lived refresh tokens (30 day expiry), issued by Supabase Auth.
- **Token storage**: Refresh tokens are stored in `httpOnly`, `Secure`, `SameSite=Lax` cookies — never in `localStorage` — to reduce exposure to XSS-based token theft.
- **Session verification**: Every API route verifies the JWT signature and expiry server-side before processing a request. Expired or invalid tokens result in `401 Unauthorized`.
- **Password policy**: Minimum length and complexity enforced at signup; passwords are never logged or stored in plaintext.
- **Multi-factor authentication (MFA)**: Supported for admin and staff accounts as an additional verification step; recommended for all users with elevated privileges.
- **Account lockout / rate limiting**: Repeated failed login attempts are throttled per account and per IP to mitigate credential-stuffing and brute-force attacks.
- **Password reset**: Time-limited, single-use reset tokens delivered via email (Resend); tokens are invalidated after use or expiry.

## Authorization

Authorization is enforced at multiple layers so that no single control failure exposes data or actions.

- **Defense in depth**: Authorization checks exist at the API layer (business logic), the database layer (Row-Level Security), and the UI layer (conditional rendering). The database layer is the source of truth; UI and API checks are conveniences, not the sole safeguard.
- **Principle of least privilege**: Users and service accounts are granted only the permissions required for their role or function.
- **Resource ownership checks**: Every request that reads or mutates a resource verifies the resource belongs to the requester's organization and that the requester's role permits the action.
- **Deny by default**: Access is denied unless an explicit policy or role grants it.

## Role-Based Access Control (RBAC)

DirectoryOS implements RBAC scoped per organization (tenant):

- **Roles**: Typical roles include `owner`, `admin`, `member`, and `viewer` (read-only) within an organization, plus an internal `platform_admin` role for staff operating across tenants.
- **Role storage**: Role assignments are stored per user, per organization (a user may hold different roles in different organizations) and are included as claims validated on every request.
- **Permission checks**: API routes and RLS policies evaluate the caller's role for the target organization before allowing an action (e.g., only `owner`/`admin` can invite users, manage billing, or delete an organization).
- **Privilege escalation prevention**: Role changes require an existing `owner`/`admin` to perform the change; users cannot self-assign elevated roles.
- **Internal staff access**: `platform_admin` actions that cross tenant boundaries (e.g., support access) are explicitly scoped, time-limited where possible, and audit-logged.
- **Review cadence**: Role assignments and permission mappings are reviewed periodically as part of the security audit cycle.

## Tenant Isolation

DirectoryOS is a multi-tenant application with shared infrastructure and strict logical data isolation.

- **Organization-scoped schema**: Every tenant-owned table includes an `organization_id` foreign key.
- **Row-Level Security (RLS)**: PostgreSQL RLS policies enforce that a query can only return or modify rows belonging to the caller's organization, based on the verified JWT claims. RLS is enforced at the database layer, so isolation holds even if application-layer checks are bypassed or contain bugs.
- **No cross-tenant queries by default**: Application code and ORM (Prisma) queries always scope by `organization_id`; RLS acts as a backstop against missing or incorrect scoping.
- **Tenant context propagation**: The active organization context is derived from the authenticated session, not from client-supplied identifiers, to prevent tenant-switching via request tampering.
- **Storage isolation**: File storage buckets/paths are namespaced by organization ID with access policies mirroring database RLS.
- **Testing**: Cross-tenant access attempts are covered by automated tests to catch isolation regressions before release.

## Secrets Management

- **No secrets in source code**: API keys, database credentials, and signing secrets are never committed to the repository.
- **Environment variables**: Secrets are stored as environment variables managed by Vercel (per-environment: development, staging, production) and Supabase project settings.
- **Least exposure**: Secrets required only server-side are never prefixed for client bundle exposure (e.g., avoiding `NEXT_PUBLIC_` for sensitive values); only genuinely public keys use client-exposed variables.
- **Rotation**: Credentials for third-party integrations (Stripe, Resend, Mapbox, AI providers) are rotated on a regular schedule and immediately upon suspected compromise or personnel offboarding.
- **Scanning**: GitHub secret scanning is enabled on the repository to detect accidental secret commits; any detected secret is rotated immediately, not just removed from history.
- **Webhook secrets**: Incoming webhooks (e.g., Stripe) are verified using signing secrets; requests failing signature verification are rejected.

## Encryption

- **Encryption at rest**: Database and file storage are encrypted at rest by the underlying Supabase infrastructure.
- **Encryption in transit**: All traffic (client-to-edge, edge-to-application, application-to-database, and third-party integrations) is transmitted over HTTPS/TLS; plaintext HTTP is not supported.
- **Password hashing**: User passwords are hashed with bcrypt (via Supabase Auth) — never stored or logged in plaintext.
- **Sensitive field handling**: Highly sensitive fields (e.g., payment details) are never stored directly; payment processing is delegated to Stripe, which is PCI-DSS compliant, so DirectoryOS avoids handling raw card data.
- **Key management**: Encryption keys and signing secrets are managed by the underlying platform providers (Supabase, Vercel) and are not handled or stored by application code.

## Audit Logging

- **What is logged**: Authentication events (login, logout, password reset, MFA changes), authorization-sensitive actions (role changes, invitations, deletions), billing changes, and administrative/staff access to tenant data.
- **Log content**: Actor (user ID), organization/tenant ID, action, target resource, timestamp, and originating IP/user agent where relevant. Logs avoid capturing sensitive payloads (e.g., passwords, full payment details).
- **Immutability**: Audit records use soft-delete/append-only patterns rather than hard deletes, preserving a durable trail for investigations.
- **Retention**: Audit logs are retained for a defined period sufficient for security investigations and compliance obligations, then archived or purged per data retention policy.
- **Aggregation & monitoring**: Application logs, error tracking (Sentry), and database logs (Supabase) are reviewed for anomalous access patterns; alerting is configured for repeated authorization failures or unusual cross-tenant access attempts.
- **Access to logs**: Audit log access itself is restricted to authorized personnel and is treated as a sensitive resource.

## API Security

- **Authentication required**: All non-public API routes require a valid JWT; requests without one receive `401 Unauthorized`.
- **Authorization enforced per request**: Every route validates the caller's role and organization membership before performing the requested action; unauthorized requests receive `403 Forbidden`.
- **Input validation**: All request bodies are validated against schemas (Zod) before being processed; malformed or unexpected input is rejected with `422 Unprocessable Entity` or `400 Bad Request`.
- **SQL injection prevention**: Database access goes through Prisma's parameterized queries; raw/unparameterized SQL is avoided.
- **Output encoding / XSS protection**: Rendered content is escaped by default (React), and a Content Security Policy (CSP) limits script execution sources.
- **CSRF protection**: State-changing requests use CSRF tokens or rely on `SameSite` cookie protections combined with origin checks.
- **CORS**: Cross-origin requests are restricted to known, allow-listed origins per environment.
- **Rate limiting**: Requests are throttled per user and per IP (e.g., 100 req/min per user, 1000 req/min per IP) to mitigate abuse, scraping, and denial-of-service attempts.
- **Consistent error responses**: Errors follow a standard shape (`error.code`, `error.message`, `error.details`) and avoid leaking internal implementation details, stack traces, or sensitive data in production responses.
- **Webhook verification**: Inbound webhooks (Stripe, etc.) validate signatures before processing.
- **Versioning & deprecation**: Backward-compatible changes are preferred; breaking changes are deprecated with advance notice, avoiding security regressions from client/server mismatches.

## OWASP Considerations

DirectoryOS's controls are mapped against the OWASP Top 10 (Web Application Security Risks):

| OWASP Risk | Mitigation in DirectoryOS |
|---|---|
| Broken Access Control | RLS at the database layer, per-request authorization checks, deny-by-default policies, resource ownership validation |
| Cryptographic Failures | TLS everywhere, bcrypt password hashing, encryption at rest via Supabase, no custom/home-grown crypto |
| Injection | Prisma parameterized queries, Zod input validation, no raw SQL string concatenation |
| Insecure Design | Threat modeling of multi-tenant boundaries, least-privilege RBAC, defense-in-depth authorization |
| Security Misconfiguration | Environment-specific configuration, secrets never in source, CSP and security headers, hardened defaults |
| Vulnerable and Outdated Components | Dependency updates tracked, automated vulnerability scanning of dependencies before adoption |
| Identification and Authentication Failures | Supabase Auth, short-lived JWTs, httpOnly refresh cookies, MFA support, account lockout/rate limiting |
| Software and Data Integrity Failures | Webhook signature verification, CI checks (lint, type-check, tests) gating deploys, code review requirements |
| Security Logging and Monitoring Failures | Audit logging of sensitive actions, Sentry error tracking, anomaly alerting |
| Server-Side Request Forgery (SSRF) | Outbound requests to third-party services use fixed, trusted endpoints; user-supplied URLs are not fetched server-side without validation |

## Compliance (GDPR/CCPA)

- **Lawful basis & consent**: Personal data collection is limited to what's necessary for the service; consent is obtained where required (e.g., marketing communications, cookies/tracking via Google Analytics/Clarity).
- **Data subject rights**: Support for access, correction, deletion (right to erasure), and data portability requests for both GDPR (EU) and CCPA (California) data subjects.
- **Data minimization**: Only necessary PII is collected and retained; optional fields are clearly distinguished from required ones.
- **Right to deletion**: Account/organization deletion requests remove or anonymize personal data within a defined timeframe, subject to legal/financial record-keeping requirements (e.g., Stripe billing records).
- **Data portability**: Users can export their data in a structured, commonly used format on request.
- **Processor agreements**: Data Processing Agreements (DPAs) are maintained with third-party processors that handle personal data (Supabase, Stripe, Resend, Vercel, analytics providers, AI providers).
- **Data residency**: Data storage location is documented so cross-border transfer requirements (e.g., EU-U.S. data transfer mechanisms) can be assessed.
- **Breach notification**: A documented incident response process ensures affected users and, where legally required, regulators are notified within applicable timeframes in the event of a data breach.
- **"Do Not Sell/Share" (CCPA)**: DirectoryOS does not sell personal information; if third-party advertising/analytics integrations constitute a "share" under CCPA, an opt-out mechanism is provided.
- **Privacy policy**: A public privacy policy describes what data is collected, why, how long it is retained, and how users can exercise their rights.

## Reporting a Vulnerability

If you discover a security vulnerability in DirectoryOS, please report it responsibly rather than opening a public issue. Provide a clear description, steps to reproduce, and any relevant logs or proof-of-concept details so the issue can be triaged and remediated promptly.
