# DirectoryOS MVP Implementation Order

## Purpose

This document defines the recommended, linear build order for a **single developer** implementing the MVP backlog defined in `docs/Engineering/MVP_BACKLOG.md`. The order respects all issue dependencies, follows the phased structure in `docs/Product/02_MVP.md`, and keeps scope strictly within MVP (no Phase 2/3 work from `docs/Product/03_ROADMAP.md` or `docs/Product/04_IDEAS.md`).

Total: **8 epics / 42 issues** across **5 milestones**.

---

## Milestone 1: Core Infrastructure (Weeks 1-3)

Foundational work. Nothing else can start until this milestone is functional, since every later feature depends on the database, auth, and deployment pipeline.

| Order | Issue | Complexity | Depends On |
|-------|-------|------------|------------|
| 1 | E1-1: Scaffold Next.js + TypeScript + Tailwind + shadcn/ui project | S | — |
| 2 | E1-2: Provision Supabase project (Postgres, Auth, Storage) | S | E1-1 |
| 3 | E2-1: Set up Prisma ORM and migration workflow | S | E1-2 |
| 4 | E2-2: Implement `organizations` table + RLS | S | E2-1 |
| 5 | E2-3: Implement `users` table + RLS | S | E2-2 |
| 6 | E1-3: Configure environment management (staging/production) | XS | E1-2 |
| 7 | E1-4: Vercel deployment (staging + production) with GitHub Actions CI | M | E1-1, E1-3 |
| 8 | E1-5: Integrate Sentry monitoring and logging | S | E1-4 |
| 9 | E2-4: Implement `categories` table | XS | E2-2 |
| 10 | E2-5: Implement `listings` table + RLS | M | E2-2, E2-4 |
| 11 | E2-6: Implement `listings_audit` table | S | E2-5 |
| 12 | E2-7: Implement `leads` table | S | E2-5 |
| 13 | E2-8: Implement `analytics_events` table | S | E2-5 |
| 14 | E3-1: Implement signup/login/logout API endpoints | M | E2-3, E1-2 |
| 15 | E3-2: Implement JWT session management with refresh/token rotation | M | E3-1 |
| 16 | E3-3: Implement password reset flow via Resend | S | E3-1 |
| 17 | E3-4: Implement RBAC middleware (admin/editor/viewer) | M | E3-1, E2-3 |
| 18 | E3-5: Implement organization multi-tenancy scoping across API | M | E3-4, E2-5 |

**Milestone 1 exit criteria**: A deployed app where a user can sign up, log in, recover a password, and where every API request is authenticated, role-checked, and organization-scoped, backed by a fully migrated database schema.

---

## Milestone 2: Listing Management (Weeks 4-6)

| Order | Issue | Complexity | Depends On |
|-------|-------|------------|------------|
| 19 | E4-1: Implement Listings CRUD API endpoints | L | E2-5, E3-5 |
| 20 | E4-2: Build listing dashboard table view (search, filter, sort, pagination) | L | E4-1 |
| 21 | E4-3: Implement bulk actions on listing dashboard | M | E4-2 |
| 22 | E4-4: Build create/edit listing form with validation | L | E4-1, E2-4 |
| 23 | E4-5: Implement Mapbox address autocomplete | M | E4-4 |
| 24 | E4-6: Implement image upload gallery via Supabase Storage | M | E4-4, E1-2 |
| 25 | E4-7: Implement auto-save draft protection | S | E4-4 |
| 26 | E4-8: Implement listing preview mode | S | E4-4 |
| 27 | E5-1: Implement CSV bulk import upload + mapping wizard | L | E4-1 |
| 28 | E5-2: Implement duplicate detection during import | M | E5-1 |
| 29 | E5-3: Implement import progress tracking, error reporting, and rollback | L | E5-1 |
| 30 | E5-4: Implement CSV bulk export | S | E4-1 |

**Milestone 2 exit criteria**: A user can create, edit, search, sort, bulk-import (10,000+ rows), and export listings end-to-end, matching `docs/Product/02_MVP.md` Success Criteria for CRUD and bulk import.

---

## Milestone 3: AI Integration (Weeks 7-9)

| Order | Issue | Complexity | Depends On |
|-------|-------|------------|------------|
| 31 | E6-1: Integrate Claude API for single-listing description generation | M | E4-1 |
| 32 | E6-2: Implement bulk AI description generation with rate limiting and version history | L | E6-1 |
| 33 | E6-3: Implement SEO analysis engine | M | E4-1 |
| 34 | E6-4: Implement content quality scoring | M | E4-1 |

**Milestone 3 exit criteria**: AI description generation (single + bulk), SEO analysis, and content quality scoring are functional and meet the `<2s` generation performance target from `docs/Product/02_MVP.md`.

---

## Milestone 4: Analytics & Dashboard (Weeks 10-11)

| Order | Issue | Complexity | Depends On |
|-------|-------|------------|------------|
| 35 | E7-1: Implement Analytics API endpoints | L | E2-8, E4-1 |
| 36 | E7-2: Build overview dashboard (key metrics, recent activity feed) | M | E7-1 |
| 37 | E7-3: Build listing analytics view | M | E7-1 |
| 38 | E7-4: Build search analytics view | M | E7-1 |
| 39 | E7-5: Build reports (monthly PDF, custom date ranges, comparisons, export) | L | E7-1 |

**Milestone 4 exit criteria**: Dashboard, listing analytics, search analytics, and reports all load within the `<1s` performance target and display real seeded data.

---

## Milestone 5: Lead Capture (Weeks 11-12)

| Order | Issue | Complexity | Depends On |
|-------|-------|------------|------------|
| 40 | E8-1: Implement Leads API endpoints | M | E2-7, E4-1 |
| 41 | E8-2: Build embedded lead capture form with spam protection | M | E8-1 |
| 42 | E8-3: Build lead management list (status, quality score, export, bulk actions) | M | E8-1 |
| 43 | E8-4: Implement lead notifications (email alert, in-app, digest) | M | E8-1, E1-2 |

**Milestone 5 exit criteria**: Leads can be captured from a public form, managed, exported, and trigger notifications — completing all MVP Success Criteria in `docs/Product/02_MVP.md`.

---

## Notes on Sequencing Rationale

- **Infrastructure before features**: E1 and E2 must complete first because every feature epic reads/writes through the database and deployment pipeline they establish.
- **Auth before anything user-facing**: E3 blocks E4-E8 because `docs/Product/02_MVP.md` requires all endpoints to be organization-scoped and role-checked from day one — retrofitting auth later would violate the Security Requirements in `02_MVP.md`.
- **Listings before AI/Analytics/Leads**: E6, E7, and E8 all operate on listing data or listing-derived events, so E4-1 (Listings CRUD) is a hard prerequisite for all three.
- **Bulk import/export (E5) can run in parallel conceptually with listing UI (E4-2 onward)** but is sequenced after the core listing form work since import relies on the same validation rules built in E4-4.
- **AI (E6), Analytics (E7), and Leads (E8) epics are independent of each other** once E4-1 exists; they are ordered per the phase windows in `docs/Product/02_MVP.md` (1C → 1D → 1E) but could be reordered relative to each other without breaking dependencies if priorities shift, since no cross-epic dependency exists between E6, E7, and E8.
- **This is a single-developer plan**: no issues are scheduled concurrently. If additional developers become available, E5 (Import/Export) and E6-E8 (AI, Analytics, Leads) are the best candidates for parallelization since they only share the E4-1 dependency.
