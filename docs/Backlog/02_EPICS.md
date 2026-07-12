# Epics

Each epic below should be created as a GitHub Issue labeled `type:epic`, assigned to its milestone, and used as a tracking/parent issue for its child issues (see `04_ISSUES.md`). Reference `05_DEPENDENCIES.md` for blocking relationships between epics.

## EPIC-1: Product Documentation Closure
- **Milestone**: M0
- **Labels**: `type:epic`, `phase:mvp`, `area:docs`, `priority:p0-blocker`
- **Source**: `docs/Product/01_PROJECT.md`, `docs/Product/PRODUCT_REVIEW.md`
- **Summary**: Close the documentation gaps identified in `PRODUCT_REVIEW.md` (personas, user stories, pricing, metrics, AI governance, directory-vs-organization model, billing data model) before database schema and architecture are locked.

## EPIC-2: Authentication & Multi-Tenancy
- **Milestone**: M1
- **Labels**: `type:epic`, `phase:mvp`, `area:auth`, `priority:p0-blocker`
- **Source**: `docs/Product/02_MVP.md` Phase 1A.1, `docs/Architecture/DATABASE.md` §users/organizations, `docs/Architecture/API.md` Authentication Endpoints
- **Summary**: Email/password auth, organization multi-tenancy, RBAC (admin/editor/viewer), JWT session management, password reset, and tenant isolation via RLS.

## EPIC-3: Database Foundation & RLS
- **Milestone**: M1
- **Labels**: `type:epic`, `phase:mvp`, `area:database`, `priority:p0-blocker`
- **Source**: `docs/Architecture/DATABASE.md`
- **Summary**: Create all MVP-subset tables (`organizations`, `users`, `listings`, `leads`, `analytics_events`, `listings_audit`, `categories`, `api_keys`), indexes, and Row-Level Security policies; stand up the Prisma migrations pipeline.

## EPIC-4: Deployment & CI/CD Infrastructure
- **Milestone**: M1
- **Labels**: `type:epic`, `phase:mvp`, `area:infra`, `priority:p0-blocker`
- **Source**: `docs/Automation/AUTOMATION.md`, `docs/Architecture/ARCHITECTURE.md` CI/CD Pipeline
- **Summary**: Vercel + Supabase environments, GitHub Actions workflows (lint, type-check, test, security scan, deploy), Dependabot, pre-commit hooks, Sentry.

## EPIC-5: Listing Dashboard & CRUD
- **Milestone**: M2
- **Labels**: `type:epic`, `phase:mvp`, `area:listings`, `priority:p1-high`
- **Source**: `docs/Product/02_MVP.md` Phase 1B.1–1B.2, `docs/Architecture/API.md` Listings Endpoints
- **Summary**: Listings table view, create/edit forms, Mapbox autocomplete, image upload, auto-save, rich text editor, preview mode, and the core Listings REST API.

## EPIC-6: Bulk Import/Export
- **Milestone**: M2
- **Labels**: `type:epic`, `phase:mvp`, `area:import-export`, `priority:p1-high`
- **Source**: `docs/Product/02_MVP.md` Phase 1B.3–1B.4
- **Summary**: CSV upload/validation, column mapping wizard, duplicate detection, progress tracking, error reporting, rollback, and CSV export with audit trail.

## EPIC-7: AI Description Generation
- **Milestone**: M3
- **Labels**: `type:epic`, `phase:mvp`, `area:ai`, `priority:p1-high`
- **Source**: `docs/Product/02_MVP.md` Phase 1C.1, `docs/Architecture/API.md` AI Endpoints, `docs/Automation/AUTOMATION.md` AI Integration Automation
- **Summary**: Claude API integration, category-specific prompts, one-click single-listing generation, human-review gate, version history. SEO analysis, content scoring, and bulk generation are deferred to `EPIC-12`.

## EPIC-8: Analytics Dashboard (MVP subset)
- **Milestone**: M4
- **Labels**: `type:epic`, `phase:mvp`, `area:analytics`, `priority:p1-high`
- **Source**: `docs/Product/02_MVP.md` Phase 1D.1–1D.2, `docs/Architecture/API.md` Analytics Endpoints
- **Summary**: Overview dashboard and per-listing analytics (views, CTR, leads, device breakdown). Search analytics and downloadable/scheduled reports are deferred to `EPIC-12`.

## EPIC-9: Lead Capture & Management
- **Milestone**: M5
- **Labels**: `type:epic`, `phase:mvp`, `area:leads`, `priority:p1-high`
- **Source**: `docs/Product/02_MVP.md` Phase 1E, `docs/Architecture/API.md` Leads Endpoints
- **Summary**: Embedded lead capture form with reCAPTCHA, lead list/status workflow, automated quality scoring, and lead export. Notifications/digests are deferred to `EPIC-12`.

## EPIC-10: Security, Compliance & Observability
- **Milestone**: cross-cutting (M1–M5, see `06_IMPLEMENTATION_ORDER.md`)
- **Labels**: `type:epic`, `phase:mvp`, `area:security`, `priority:p0-blocker`
- **Source**: `docs/Architecture/ARCHITECTURE.md` Security Architecture, `docs/Product/02_MVP.md` Security/Compliance Requirements
- **Summary**: HTTPS/CORS/rate limiting, JWT rotation, CSRF, input validation, audit logging, GDPR/CCPA export & deletion workflows, ToS/Privacy Policy/cookie consent, uptime monitoring.

## EPIC-11: Testing & QA Automation
- **Milestone**: cross-cutting (M1–M5, see `06_IMPLEMENTATION_ORDER.md`)
- **Labels**: `type:epic`, `phase:mvp`, `area:testing`, `priority:p0-blocker`
- **Source**: `docs/Automation/AUTOMATION.md` Testing Automation
- **Summary**: Jest unit tests, Playwright integration tests, Lighthouse CI, and validation of the MVP performance targets in `02_MVP.md`.

## EPIC-12: Phase 2 Deferred Features Backlog
- **Milestone**: M6
- **Labels**: `type:epic`, `phase:2`, `priority:p3-low`
- **Source**: `docs/Product/PRODUCT_REVIEW.md` §3, `docs/Product/03_ROADMAP.md` Phase 2, `docs/Product/04_IDEAS.md`
- **Summary**: Tracking epic for all MVP-deferred items (SEO analysis, content scoring, bulk AI generation, search analytics, reports, notifications) plus `03_ROADMAP.md` Phase 2 features (social, email, maps, lead scoring, advanced reporting) and top-voted `04_IDEAS.md` candidates. Not built during MVP.
