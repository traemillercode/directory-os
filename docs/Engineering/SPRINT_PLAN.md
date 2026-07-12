# DirectoryOS MVP Sprint Plan

## Purpose

This document converts the approved MVP backlog (`docs/Engineering/MVP_BACKLOG.md`) and its build sequence (`docs/Engineering/IMPLEMENTATION_ORDER.md`) into a concrete sprint-by-sprint execution plan.

Scope is strictly limited to the 43 issues (E1-1 through E8-4) already defined in `docs/Engineering/MVP_BACKLOG.md`. No Phase 2 (`docs/Product/03_ROADMAP.md`) or idea-stage (`docs/Product/04_IDEAS.md`) work is included.

## Assumptions

- **Team size**: one full-time developer, working solo end to end.
- **Capacity**: 2-week sprints, ~10 working days per sprint, no concurrent work streams.
- **Quality bar**: production quality — every issue must satisfy the Common Definition of Done in `docs/Engineering/MVP_BACKLOG.md` §5 (tests passing, lint/type-check clean, no secrets, docs updated, CI green) in addition to its own acceptance criteria.
- **Estimates**: derived from the complexity scale in `docs/Engineering/MVP_BACKLOG.md` §4 (XS ≈ 0.5d, S ≈ 1d, M ≈ 2.5d, L ≈ 4.5d), applied per issue and rolled up per sprint.
- **Sequencing**: strictly respects the dependency graph in `docs/Engineering/MVP_BACKLOG.md` §6 and the order in `docs/Engineering/IMPLEMENTATION_ORDER.md`. No issue is scheduled before its dependencies are done.

---

## Sprint 0 — Project Setup & Planning

**Objective**: Stand up the project management scaffolding so that all subsequent sprints can execute against real GitHub issues, without writing any product code.

**GitHub issues included**: None (this sprint produces the GitHub artifacts — epics, milestones, labels, and issues — described in `docs/Engineering/MVP_BACKLOG.md` §1-§3; it does not implement any MVP issue).

**Dependencies**: None.

**Estimated duration**: 2-3 working days.

**Deliverables**:
- GitHub milestones M1-M5 created per `docs/Engineering/MVP_BACKLOG.md` §2.
- GitHub labels (type, area, complexity, priority, phase) created per §3.
- All 43 issues (E1-1 … E8-4) created with their labels, descriptions, acceptance criteria, and dependency links.
- Repository project board configured to track issues through the sprint sequence below.

**Exit criteria**: Every issue in the MVP backlog exists in GitHub, correctly labeled, milestoned, and linked to its dependencies; the board reflects Sprint 1 as the next unit of work.

---

## Sprint 1 — Infrastructure & Database Bootstrap

**Objective**: Stand up the deployable application skeleton, environments, CI/CD, monitoring, and the first tier of the database schema (organizations, users).

**GitHub issues included**:
- E1-1: Scaffold Next.js + TypeScript + Tailwind + shadcn/ui project
- E1-2: Provision Supabase project (Postgres, Auth, Storage)
- E1-3: Configure environment management (staging/production)
- E1-4: Vercel deployment (staging + production) with GitHub Actions CI
- E1-5: Integrate Sentry monitoring and logging
- E2-1: Set up Prisma ORM and migration workflow
- E2-2: Implement `organizations` table + RLS
- E2-3: Implement `users` table + RLS

**Dependencies**: None outside this sprint (E1-1 is the root of the whole project).

**Estimated duration**: 2 weeks.

**Deliverables**:
- Deployed staging and production environments on Vercel with auto-deploy on merge and PR previews.
- GitHub Actions CI running lint, type-check, and tests on every PR, blocking merge on failure.
- Sentry capturing errors from both environments.
- Prisma connected to Supabase Postgres with a working migration workflow.
- `organizations` and `users` tables live with Row-Level Security enforced.

**Exit criteria**: A test PR shows green CI and a working preview deployment; a migration applies cleanly on a fresh database; RLS is verified to block cross-organization reads; no secrets are committed (secret scan clean).

---

## Sprint 2 — Database Completion & Core Auth

**Objective**: Finish the database schema and deliver signup/login/session management.

**GitHub issues included**:
- E2-4: Implement `categories` table
- E2-5: Implement `listings` table + RLS
- E2-6: Implement `listings_audit` table
- E2-7: Implement `leads` table
- E2-8: Implement `analytics_events` table
- E3-1: Implement signup/login/logout API endpoints
- E3-2: Implement JWT session management with refresh/token rotation

**Dependencies**: Sprint 1 (E1-2, E2-1, E2-2, E2-3).

**Estimated duration**: 2 weeks.

**Deliverables**:
- Full MVP schema in place: `categories`, `listings`, `listings_audit`, `leads`, `analytics_events`, all with required RLS/indexes.
- `POST /api/auth/signup`, `/login`, `/logout` matching `docs/Architecture/API.md` contracts.
- `POST /api/auth/refresh` with token rotation.

**Exit criteria**: Listing update produces exactly one audit row in tests; FK cascade verified on listing delete; signup/login/logout/refresh endpoints pass integration tests matching documented request/response shapes; token rotation invalidates prior refresh tokens.

---

## Sprint 3 — Auth Hardening & Listings API

**Objective**: Complete authentication/authorization (password reset, RBAC, multi-tenancy) and deliver the Listings CRUD API that all downstream feature epics depend on.

**GitHub issues included**:
- E3-3: Implement password reset flow via Resend
- E3-4: Implement RBAC middleware (admin/editor/viewer)
- E3-5: Implement organization multi-tenancy scoping across API
- E4-1: Implement Listings CRUD API endpoints

**Dependencies**: Sprint 2 (E3-1, E2-3, E2-5).

**Estimated duration**: 2 weeks.

**Deliverables**:
- End-to-end password reset flow via Resend.
- RBAC middleware enforcing admin/editor/viewer permissions on all routes.
- Organization-scoping enforced defense-in-depth across every API query.
- `GET/POST/PATCH/DELETE /api/listings` and `/api/listings/:id` fully implemented with pagination, filtering, validation, audit writes, and soft delete.

**Exit criteria**: Each role tested against one allowed and one disallowed action; a test proves user A cannot access org B's data; all five Listings endpoints pass integration tests matching `docs/Architecture/API.md`. **Milestone 1 (Core Infrastructure) is fully complete at the end of this sprint.**

---

## Sprint 4 — Listing Dashboard & Authoring UI

**Objective**: Deliver the listing dashboard (search/filter/sort/bulk actions) and the create/edit listing form with geolocation support.

**GitHub issues included**:
- E4-2: Build listing dashboard table view (search, filter, sort, pagination)
- E4-3: Implement bulk actions on listing dashboard
- E4-4: Build create/edit listing form with validation
- E4-5: Implement Mapbox address autocomplete

**Dependencies**: Sprint 3 (E4-1); E4-4 also depends on E2-4 (categories, delivered in Sprint 2).

**Estimated duration**: 2 weeks.

**Deliverables**:
- Listing dashboard with search, sort (date/name/lead count), pagination, and quick stats, performing under 500ms at 1,000+ listings.
- Bulk status-change/delete actions respecting RBAC.
- Create/edit form covering all MVP fields with client- and server-side validation and rich text description editor.
- Mapbox-powered address autocomplete populating latitude/longitude, with manual entry fallback.

**Exit criteria**: Search response measured under 500ms with 1,000 seeded listings; bulk action tested against 10+ selected rows and blocked for Viewer role; invalid form submissions blocked with field-level errors; latitude/longitude verified to persist correctly.

---

## Sprint 5 — Listing Finishing Touches & Bulk Import/Export

**Objective**: Complete the remaining listing authoring features (images, auto-save, preview) and deliver bulk CSV import/export.

**GitHub issues included**:
- E4-6: Implement image upload gallery via Supabase Storage
- E4-7: Implement auto-save draft protection
- E4-8: Implement listing preview mode
- E5-1: Implement CSV bulk import upload + mapping wizard
- E5-2: Implement duplicate detection during import
- E5-3: Implement import progress tracking, error reporting, and rollback
- E5-4: Implement CSV bulk export

**Dependencies**: Sprint 4 (E4-4, E4-1); E4-6 also depends on E1-2 (Supabase Storage, delivered in Sprint 1).

**Estimated duration**: 2 weeks.

**Deliverables**:
- Multi-image upload with drag-to-reorder, stored in Supabase Storage.
- Auto-save drafts with reload recovery; listing preview mode reflecting unsaved state.
- CSV import with column-mapping wizard, duplicate detection (skip/merge/create), real-time progress, row-level error reporting, and full rollback.
- CSV export (all or filtered listings), Excel-compatible, with an audit trail entry.

**Exit criteria**: A sample 100-row CSV imports correctly end-to-end; a 10,000+ row import completes reliably per `docs/Product/02_MVP.md` Success Criteria; rollback fully reverts a test import with zero orphaned records; exported file opens correctly in Excel/Google Sheets. **Milestone 2 (Listing Management) is fully complete at the end of this sprint.**

---

## Sprint 6 — AI Integration

**Objective**: Deliver AI-assisted description generation, SEO analysis, and content quality scoring.

**GitHub issues included**:
- E6-1: Integrate Claude API for single-listing description generation
- E6-2: Implement bulk AI description generation with rate limiting and version history
- E6-3: Implement SEO analysis engine
- E6-4: Implement content quality scoring

**Dependencies**: Sprint 3/5 (E4-1, Listings CRUD).

**Estimated duration**: 2 weeks.

**Deliverables**:
- `POST /api/ai/generate-description` generating human-review-required content in under 2 seconds.
- Bulk generation across selected listings with rate limiting and retained version history.
- SEO analysis engine (title/keywords, meta description, keyword density, readability) with actionable suggestions and batch audit.
- `POST /api/ai/content-score` scoring completeness, image quality, description length, and category fit.

**Exit criteria**: Generation latency measured and logged against the 2s target; rate limiter verified to reject/queue over-threshold requests; SEO output validated against known-good and known-bad samples; content-score factors unit-tested independently. **Milestone 3 (AI Integration) is fully complete at the end of this sprint.**

---

## Sprint 7 — Analytics & Dashboard

**Objective**: Deliver the Analytics API and all reporting surfaces (overview dashboard, listing analytics, search analytics, reports).

**GitHub issues included**:
- E7-1: Implement Analytics API endpoints
- E7-2: Build overview dashboard (key metrics, recent activity feed)
- E7-3: Build listing analytics view
- E7-4: Build search analytics view
- E7-5: Build reports (monthly PDF, custom date ranges, comparisons, export)

**Dependencies**: Sprint 2 (E2-8), Sprint 3 (E4-1).

**Estimated duration**: 2 weeks.

**Deliverables**:
- All four documented analytics endpoints (`/analytics/dashboard`, `/analytics/listing/:id`, `/analytics/search-terms`, `/analytics/report`), organization-scoped, responding under 1s.
- Overview dashboard with key metrics, trends, quick actions, and recent activity feed.
- Listing analytics (views, CTR, leads, time-to-first-lead, device/traffic breakdown).
- Search analytics (keywords, average position, impressions, map performance).
- Monthly PDF reports with custom date ranges, month-over-month comparisons, and data export.

**Exit criteria**: Response times measured against the 1s target with realistic seeded data; dashboard loads under 1s with 1,000 seeded listings; each analytics metric verified against seeded `analytics_events` data; generated PDF verified to render correctly and match on-screen data. **Milestone 4 (Analytics & Dashboard) is fully complete at the end of this sprint.**

---

## Sprint 8 — Lead Capture & Management

**Objective**: Deliver lead capture, management, and notifications, completing all MVP Success Criteria.

**GitHub issues included**:
- E8-1: Implement Leads API endpoints
- E8-2: Build embedded lead capture form with spam protection
- E8-3: Build lead management list (status, quality score, export, bulk actions)
- E8-4: Implement lead notifications (email alert, in-app, digest)

**Dependencies**: Sprint 2 (E2-7), Sprint 3 (E4-1), Sprint 1 (E1-2, Resend/Supabase infra).

**Estimated duration**: 2 weeks.

**Deliverables**:
- `GET/POST/PATCH /api/leads` and `/api/leads/export` matching documented contracts.
- Embedded lead capture form on listing pages with spam protection (reCAPTCHA or equivalent) and optional double opt-in.
- Lead management list with status, quality score, CSV export, and bulk actions (mark contacted, delete).
- Email alerts, in-app notifications, and daily digest for new leads.

**Exit criteria**: Endpoints covered by integration tests matching `docs/Architecture/API.md`; spam protection verified to reject a scripted submission; bulk action and export verified against 20+ seeded leads; all three notification channels verified to fire on a test lead submission. **Milestone 5 (Lead Capture) is fully complete — all MVP Success Criteria in `docs/Product/02_MVP.md` are met and the product is ready for MVP launch.**

---

## Summary

| Sprint | Focus | Issues | Duration |
|--------|-------|--------|----------|
| Sprint 0 | Project setup & planning | — (GitHub scaffolding) | 2-3 days |
| Sprint 1 | Infra & database bootstrap | E1-1..E1-5, E2-1..E2-3 | 2 weeks |
| Sprint 2 | Database completion & core auth | E2-4..E2-8, E3-1, E3-2 | 2 weeks |
| Sprint 3 | Auth hardening & Listings API | E3-3, E3-4, E3-5, E4-1 | 2 weeks |
| Sprint 4 | Listing dashboard & authoring UI | E4-2..E4-5 | 2 weeks |
| Sprint 5 | Listing finishing & bulk import/export | E4-6..E4-8, E5-1..E5-4 | 2 weeks |
| Sprint 6 | AI integration | E6-1..E6-4 | 2 weeks |
| Sprint 7 | Analytics & dashboard | E7-1..E7-5 | 2 weeks |
| Sprint 8 | Lead capture & management | E8-1..E8-4 | 2 weeks |

**Total**: 43 MVP issues across 9 sprints (~16-17 weeks including Sprint 0), for one full-time developer working at production-quality standards.
