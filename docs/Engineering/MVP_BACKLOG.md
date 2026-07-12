# DirectoryOS MVP Backlog

## Purpose

This document is the complete MVP development management system for DirectoryOS. It translates the product and architecture documentation into GitHub-ready **Epics**, **Milestones**, **Labels**, **Issues**, **dependencies**, **complexity estimates**, **acceptance criteria**, and a **Definition of Done (DoD)**.

Work is scoped **strictly to MVP** as defined in `docs/Product/02_MVP.md`. No Phase 2 (`docs/Product/03_ROADMAP.md`) or Phase 3/idea-stage (`docs/Product/04_IDEAS.md`) features are included. No new architecture is invented — all technical decisions trace back to `docs/Architecture/ARCHITECTURE.md`, `docs/Architecture/DATABASE.md`, and `docs/Architecture/API.md`.

Work is organized for **one developer** working sequentially. See `docs/Engineering/IMPLEMENTATION_ORDER.md` for the recommended build order.

---

## 1. MVP Epics

| Epic | Title | MVP Phase | Source |
|------|-------|-----------|--------|
| **E1** | Core Infrastructure & Deployment | Phase 1A (Weeks 1-3) | `docs/Product/02_MVP.md` §Phase 1A #3; `docs/Architecture/ARCHITECTURE.md` |
| **E2** | Database Foundation | Phase 1A (Weeks 1-3) | `docs/Product/02_MVP.md` §Phase 1A #2; `docs/Architecture/DATABASE.md` |
| **E3** | Authentication & Authorization | Phase 1A (Weeks 1-3) | `docs/Product/02_MVP.md` §Phase 1A #1; `docs/Architecture/API.md` §Authentication Endpoints |
| **E4** | Listing Management | Phase 1B (Weeks 4-6) | `docs/Product/02_MVP.md` §Phase 1B #1-2; `docs/Architecture/API.md` §Listings |
| **E5** | Bulk Import & Export | Phase 1B (Weeks 4-6) | `docs/Product/02_MVP.md` §Phase 1B #3-4 |
| **E6** | AI Integration (Description, SEO, Content Quality) | Phase 1C (Weeks 7-9) | `docs/Product/02_MVP.md` §Phase 1C; `docs/Architecture/API.md` §AI & Content |
| **E7** | Analytics & Dashboard | Phase 1D (Weeks 10-11) | `docs/Product/02_MVP.md` §Phase 1D; `docs/Architecture/API.md` §Analytics |
| **E8** | Lead Capture & Management | Phase 1E (Weeks 11-12) | `docs/Product/02_MVP.md` §Phase 1E; `docs/Architecture/API.md` §Leads |

**Explicitly out of scope (do not create issues for these):** social media posting, email automation sequences, paid advertising management, event calendar integration, white-label customization, custom domain support, third-party integration API, mobile native apps, Google OAuth (marked "future phase" in `02_MVP.md`), `api_keys` table (only needed for third-party API access, which is out of MVP scope per `02_MVP.md` Non-Features).

---

## 2. GitHub Milestones

| Milestone | Epics Covered | Target Window (per `02_MVP.md`) |
|-----------|----------------|----------------------------------|
| **M1: Core Infrastructure** | E1, E2, E3 | Weeks 1-3 |
| **M2: Listing Management** | E4, E5 | Weeks 4-6 |
| **M3: AI Integration** | E6 | Weeks 7-9 |
| **M4: Analytics & Dashboard** | E7 | Weeks 10-11 |
| **M5: Lead Capture** | E8 | Weeks 11-12 |

---

## 3. GitHub Labels

**Type**
- `type:epic`
- `type:feature`
- `type:infra`
- `type:chore`

**Area**
- `area:infra`
- `area:database`
- `area:auth`
- `area:listings`
- `area:import-export`
- `area:ai`
- `area:analytics`
- `area:leads`

**Complexity**
- `complexity:XS`
- `complexity:S`
- `complexity:M`
- `complexity:L`
- `complexity:XL`

**Priority**
- `priority:P0` (blocking / foundational)
- `priority:P1` (core MVP feature)
- `priority:P2` (rounding out MVP feature set)

**Phase**
- `phase:1a-core-infra`
- `phase:1b-listings`
- `phase:1c-ai`
- `phase:1d-analytics`
- `phase:1e-leads`

---

## 4. Complexity Scale

| Size | Meaning (single developer) |
|------|------------------------------|
| **XS** | < 0.5 day. Config/toggle-level change. |
| **S** | 0.5-1 day. Single component/endpoint, low complexity. |
| **M** | 2-3 days. Multiple components or one non-trivial integration. |
| **L** | 4-5 days. Cross-cutting feature, multiple endpoints/tables/UI states. |
| **XL** | > 5 days (should be reconsidered for splitting). Complex integration or workflow. |

---

## 5. Epics, Issues, Dependencies, Acceptance Criteria & Definition of Done

Each issue below is written to be created as a GitHub Issue with the labels: its `type`, `area`, `phase`, `priority`, and `complexity` label.

### Common Definition of Done (applies to every issue in addition to the issue-specific DoD)

- [ ] Code implemented and matches the referenced documentation section.
- [ ] No new architecture/tables/endpoints introduced beyond what is documented in `docs/Architecture/*`.
- [ ] Unit/integration tests written and passing for the change.
- [ ] Linting and type-checking pass with no new errors.
- [ ] No hardcoded secrets; environment variables used per `docs/Product/02_MVP.md` Security Requirements.
- [ ] Code reviewed (self-review checklist if solo) against acceptance criteria.
- [ ] Relevant documentation updated if behavior differs from the source doc.
- [ ] Merged to main without breaking CI (GitHub Actions).

---

### Epic E1: Core Infrastructure & Deployment
*Milestone: M1 — Labels: `type:epic`, `area:infra`, `phase:1a-core-infra`*
*Source: `docs/Product/02_MVP.md` §Phase 1A #3; `docs/Architecture/ARCHITECTURE.md`*

#### E1-1: Scaffold Next.js + TypeScript + Tailwind + shadcn/ui project
- **Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`
- **Depends on**: none
- **Source**: `docs/Product/01_PROJECT.md` §Technology Foundation; `docs/Architecture/ARCHITECTURE.md` §Frontend Layer
- **Acceptance Criteria**:
  - [ ] Next.js 14+ App Router project initialized with TypeScript.
  - [ ] Tailwind CSS configured and building.
  - [ ] shadcn/ui installed with a base component (e.g., Button) rendering.
  - [ ] Project runs locally with a documented `npm run dev` command.
- **Definition of Done**: Common DoD + project builds with `npm run build` with zero errors.

#### E1-2: Provision Supabase project (Postgres, Auth, Storage)
- **Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`
- **Depends on**: E1-1
- **Source**: `docs/Architecture/ARCHITECTURE.md` §Backend Layer; `docs/Product/02_MVP.md` §Phase 1A #3
- **Acceptance Criteria**:
  - [ ] Supabase project created for staging and production.
  - [ ] Postgres connection string available via environment variables.
  - [ ] Supabase Auth and Storage buckets enabled.
- **Definition of Done**: Common DoD + connection verified from local app via a health-check script/route.

#### E1-3: Configure environment management (staging/production)
- **Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P0`, `complexity:XS`
- **Depends on**: E1-2
- **Source**: `docs/Product/02_MVP.md` §Phase 1A #3
- **Acceptance Criteria**:
  - [ ] `.env.example` documents all required variables.
  - [ ] Staging and production environments have separate, non-shared secrets.
  - [ ] No secrets committed to source control.
- **Definition of Done**: Common DoD + secret scan run with zero findings.

#### E1-4: Vercel deployment (staging + production) with GitHub Actions CI
- **Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`
- **Depends on**: E1-1, E1-3
- **Source**: `docs/Product/02_MVP.md` §Phase 1A #3; `docs/Product/01_PROJECT.md` §Technology Foundation
- **Acceptance Criteria**:
  - [ ] Vercel project connected to the repository with auto-deploy on merge to main.
  - [ ] Staging deployment triggers on PR/preview branches.
  - [ ] GitHub Actions workflow runs lint, type-check, and tests on every PR.
  - [ ] Failing CI blocks merge.
- **Definition of Done**: Common DoD + a test PR shows green CI and a working preview deployment URL.

#### E1-5: Integrate Sentry monitoring and logging
- **Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P1`, `complexity:S`
- **Depends on**: E1-4
- **Source**: `docs/Product/02_MVP.md` §Phase 1A #3
- **Acceptance Criteria**:
  - [ ] Sentry SDK integrated in frontend and API routes.
  - [ ] Errors from staging and production are captured with environment tags.
  - [ ] A manually triggered test error appears in the Sentry dashboard.
- **Definition of Done**: Common DoD + verified error captured in Sentry with source map support.

---

### Epic E2: Database Foundation
*Milestone: M1 — Labels: `type:epic`, `area:database`, `phase:1a-core-infra`*
*Source: `docs/Architecture/DATABASE.md`; `docs/Product/02_MVP.md` §Phase 1A #2*

#### E2-1: Set up Prisma ORM and migration workflow
- **Labels**: `type:infra`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`
- **Depends on**: E1-2
- **Source**: `docs/Architecture/ARCHITECTURE.md` §Prisma ORM
- **Acceptance Criteria**:
  - [ ] Prisma installed and connected to Supabase Postgres.
  - [ ] `prisma migrate` workflow documented and working locally and in CI.
- **Definition of Done**: Common DoD + a sample migration applies cleanly on a fresh database.

#### E2-2: Implement `organizations` table + RLS
- **Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`
- **Depends on**: E2-1
- **Source**: `docs/Architecture/DATABASE.md` §1. organizations
- **Acceptance Criteria**:
  - [ ] Table matches the schema in `DATABASE.md` (id, name, slug, timezone, industry, subscription_tier, etc.).
  - [ ] Row-Level Security enabled scoping access by `organization_id`.
  - [ ] Indexes on `slug` and `subscription_tier` created.
- **Definition of Done**: Common DoD + RLS policy verified to block cross-organization reads in a test.

#### E2-3: Implement `users` table + RLS
- **Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`
- **Depends on**: E2-2
- **Source**: `docs/Architecture/DATABASE.md` §2. users
- **Acceptance Criteria**:
  - [ ] Table matches schema (id, organization_id FK, email unique, role check constraint admin/editor/viewer, password_hash, email_verified, etc.).
  - [ ] RLS restricts users to their own organization.
- **Definition of Done**: Common DoD + role check constraint verified to reject invalid role values.

#### E2-4: Implement `categories` table
- **Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P1`, `complexity:XS`
- **Depends on**: E2-2
- **Source**: `docs/Product/02_MVP.md` §Phase 1A #2 (Categories table)
- **Acceptance Criteria**:
  - [ ] Table stores category name and hierarchy/classification needed for listing classification.
  - [ ] Seed data for common categories available for local/staging use.
- **Definition of Done**: Common DoD + seed script runs without error.

#### E2-5: Implement `listings` table + RLS
- **Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`
- **Depends on**: E2-2, E2-4
- **Source**: `docs/Architecture/DATABASE.md` §3. listings; `docs/Product/02_MVP.md` §Technical Specifications
- **Acceptance Criteria**:
  - [ ] Table includes all MVP fields: name, category, description, short_description, phone, email, website, address, latitude/longitude, hours_json, images, social_links, status (draft/published/archived).
  - [ ] RLS scopes listings by `organization_id`.
  - [ ] Indexes support search/filter/sort at 1,000+ rows per `02_MVP.md` performance targets.
- **Definition of Done**: Common DoD + query plan reviewed for the primary listing search query.

#### E2-6: Implement `listings_audit` table
- **Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P1`, `complexity:S`
- **Depends on**: E2-5
- **Source**: `docs/Product/02_MVP.md` §Phase 1A #2 (Listings_Audit table); `docs/Architecture/DATABASE.md` §6. listings_audit
- **Acceptance Criteria**:
  - [ ] Table captures change tracking (who, what, when) for listing mutations.
  - [ ] Audit rows are created automatically on listing create/update/delete.
- **Definition of Done**: Common DoD + a listing update produces exactly one corresponding audit row in tests.

#### E2-7: Implement `leads` table
- **Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`
- **Depends on**: E2-5
- **Source**: `docs/Architecture/DATABASE.md` §4. leads; `docs/Product/02_MVP.md` §Technical Specifications
- **Acceptance Criteria**:
  - [ ] Table includes: listing_id FK, name, email, phone, message, source, status (new/contacted/converted/lost), quality_score.
  - [ ] RLS scopes leads via the parent listing's organization.
- **Definition of Done**: Common DoD + foreign key cascade behavior verified when a listing is deleted.

#### E2-8: Implement `analytics_events` table
- **Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P1`, `complexity:S`
- **Depends on**: E2-5
- **Source**: `docs/Architecture/DATABASE.md` §5. analytics_events; `docs/Product/02_MVP.md` §Technical Specifications
- **Acceptance Criteria**:
  - [ ] Table includes: organization_id, event_type (view/click/lead), listing_id, metadata JSON, timestamp.
  - [ ] Table can sustain write volume needed for dashboard analytics (indexed by organization_id, listing_id, timestamp).
- **Definition of Done**: Common DoD + insert/read benchmarked against `02_MVP.md` performance targets.

---

### Epic E3: Authentication & Authorization
*Milestone: M1 — Labels: `type:epic`, `area:auth`, `phase:1a-core-infra`*
*Source: `docs/Product/02_MVP.md` §Phase 1A #1; `docs/Architecture/API.md` §Authentication Endpoints*

#### E3-1: Implement signup/login/logout API endpoints
- **Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`
- **Depends on**: E2-3, E1-2
- **Source**: `docs/Architecture/API.md` §POST /auth/signup, /auth/login, /auth/logout
- **Acceptance Criteria**:
  - [ ] `POST /api/auth/signup` creates a user + organization and returns access/refresh tokens matching the documented response shape.
  - [ ] `POST /api/auth/login` authenticates and returns tokens.
  - [ ] `POST /api/auth/logout` invalidates the session/token.
  - [ ] Passwords are hashed; plaintext passwords are never stored or logged.
- **Definition of Done**: Common DoD + endpoints match request/response contracts in `API.md` exactly.

#### E3-2: Implement JWT session management with refresh/token rotation
- **Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`
- **Depends on**: E3-1
- **Source**: `docs/Architecture/API.md` §POST /auth/refresh; `docs/Product/02_MVP.md` §Security Requirements
- **Acceptance Criteria**:
  - [ ] `POST /api/auth/refresh` issues a new access token from a valid refresh token.
  - [ ] Expired/invalid tokens are rejected with correct status codes.
  - [ ] Token rotation invalidates the previous refresh token.
- **Definition of Done**: Common DoD + rotation behavior covered by an automated test.

#### E3-3: Implement password reset flow via Resend
- **Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P1`, `complexity:S`
- **Depends on**: E3-1
- **Source**: `docs/Architecture/API.md` §POST /auth/password-reset; `docs/Product/02_MVP.md` §Account recovery
- **Acceptance Criteria**:
  - [ ] Requesting a reset sends an email via Resend with a time-limited reset link/token.
  - [ ] Submitting a valid token updates the password.
  - [ ] Expired/used tokens are rejected.
- **Definition of Done**: Common DoD + end-to-end reset flow tested with a test email account.

#### E3-4: Implement RBAC middleware (admin/editor/viewer)
- **Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`
- **Depends on**: E3-1, E2-3
- **Source**: `docs/Product/02_MVP.md` §Role-based access control (RBAC)
- **Acceptance Criteria**:
  - [ ] Admin has full access to all organization resources.
  - [ ] Editor can manage listings and view analytics but not manage users/billing.
  - [ ] Viewer has read-only access.
  - [ ] Unauthorized actions return 403 with a clear error.
- **Definition of Done**: Common DoD + each role tested against at least one allowed and one disallowed action.

#### E3-5: Implement organization multi-tenancy scoping across API
- **Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`
- **Depends on**: E3-4, E2-5
- **Source**: `docs/Product/02_MVP.md` §Organization multi-tenancy
- **Acceptance Criteria**:
  - [ ] Every authenticated request resolves the caller's `organization_id`.
  - [ ] All data queries are scoped to the caller's organization (defense in depth alongside RLS).
  - [ ] Cross-organization data access attempts are blocked and logged.
- **Definition of Done**: Common DoD + a test proves user A cannot read/write user B's organization data.

---

### Epic E4: Listing Management
*Milestone: M2 — Labels: `type:epic`, `area:listings`, `phase:1b-listings`*
*Source: `docs/Product/02_MVP.md` §Phase 1B #1-2; `docs/Architecture/API.md` §Listings*

#### E4-1: Implement Listings CRUD API endpoints
- **Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P0`, `complexity:L`
- **Depends on**: E2-5, E3-5
- **Source**: `docs/Architecture/API.md` §GET/POST/PATCH/DELETE /listings, /listings/:id
- **Acceptance Criteria**:
  - [ ] `GET /api/listings` supports pagination and filtering as documented.
  - [ ] `POST /api/listings` creates a listing with validation on required fields.
  - [ ] `GET /api/listings/:id` returns a single listing.
  - [ ] `PATCH /api/listings/:id` updates a listing and writes an audit row.
  - [ ] `DELETE /api/listings/:id` performs a soft delete (status change, not row removal).
- **Definition of Done**: Common DoD + all five endpoints covered by integration tests matching `API.md` contracts.

#### E4-2: Build listing dashboard table view (search, filter, sort, pagination)
- **Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P0`, `complexity:L`
- **Depends on**: E4-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #1 Listing Dashboard
- **Acceptance Criteria**:
  - [ ] Table displays all organization listings with search by name/category/status.
  - [ ] Sorting by date, name, and lead count works.
  - [ ] Pagination performs correctly at 1,000+ listings without UI slowdown (`<500ms` search per `02_MVP.md` performance targets).
  - [ ] Quick stats (total, active, pending, archived) are shown.
- **Definition of Done**: Common DoD + manual/automated test confirms search response under 500ms with 1,000 seeded listings.

#### E4-3: Implement bulk actions on listing dashboard
- **Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P1`, `complexity:M`
- **Depends on**: E4-2
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #1 Bulk actions
- **Acceptance Criteria**:
  - [ ] Users can select multiple listings via checkboxes.
  - [ ] At least the documented bulk operations (status change, delete) are available.
  - [ ] Bulk actions respect RBAC (Viewer cannot perform them).
- **Definition of Done**: Common DoD + bulk action tested against 10+ selected rows.

#### E4-4: Build create/edit listing form with validation
- **Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P0`, `complexity:L`
- **Depends on**: E4-1, E2-4
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #2 Create & Edit Listings
- **Acceptance Criteria**:
  - [ ] Form includes all MVP fields: name, category (multi-select), description, phone, email, website, address, hours, images, social links.
  - [ ] Client and server-side validation enforce required fields and formats (email, phone, URL).
  - [ ] Rich text editor available for the description field.
- **Definition of Done**: Common DoD + invalid submissions blocked with field-level error messages.

#### E4-5: Implement Mapbox address autocomplete
- **Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P1`, `complexity:M`
- **Depends on**: E4-4
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #2 (Address with Mapbox autocomplete); `docs/Product/01_PROJECT.md` §Technology Foundation (Mapbox)
- **Acceptance Criteria**:
  - [ ] Address field suggests results via Mapbox as the user types.
  - [ ] Selecting a suggestion populates address, latitude, and longitude.
  - [ ] Manual address entry remains possible if Mapbox is unavailable.
- **Definition of Done**: Common DoD + latitude/longitude verified to persist correctly on the listing record.

#### E4-6: Implement image upload gallery via Supabase Storage
- **Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P1`, `complexity:M`
- **Depends on**: E4-4, E1-2
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #2 (Images gallery, Image upload)
- **Acceptance Criteria**:
  - [ ] Users can upload multiple images per listing.
  - [ ] Images can be reordered via drag-to-reorder.
  - [ ] Images are stored in Supabase Storage and referenced by URL on the listing.
- **Definition of Done**: Common DoD + upload size/type validated and rejected gracefully when invalid.

#### E4-7: Implement auto-save draft protection
- **Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P2`, `complexity:S`
- **Depends on**: E4-4
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #2 (Auto-save)
- **Acceptance Criteria**:
  - [ ] Form changes are periodically saved as a draft without explicit user action.
  - [ ] Reloading the page recovers the last auto-saved draft.
- **Definition of Done**: Common DoD + verified draft recovery after a simulated page reload.

#### E4-8: Implement listing preview mode
- **Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P2`, `complexity:S`
- **Depends on**: E4-4
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #2 (Preview mode)
- **Acceptance Criteria**:
  - [ ] Users can preview how a listing will appear before publishing.
  - [ ] Preview reflects current unsaved form state.
- **Definition of Done**: Common DoD + preview verified to match published output for the same data.

---

### Epic E5: Bulk Import & Export
*Milestone: M2 — Labels: `type:epic`, `area:import-export`, `phase:1b-listings`*
*Source: `docs/Product/02_MVP.md` §Phase 1B #3-4*

#### E5-1: Implement CSV bulk import upload + mapping wizard
- **Labels**: `type:feature`, `area:import-export`, `phase:1b-listings`, `priority:P0`, `complexity:L`
- **Depends on**: E4-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #3 Bulk Import; `docs/Architecture/API.md` §POST /listings/:id/bulk-import`
- **Acceptance Criteria**:
  - [ ] Users can upload a CSV file.
  - [ ] A mapping wizard matches CSV columns to listing fields, with auto-detection where possible.
  - [ ] Uploaded data is validated before import (required fields, formats).
- **Definition of Done**: Common DoD + a sample CSV of 100 rows imports correctly end-to-end.

#### E5-2: Implement duplicate detection during import
- **Labels**: `type:feature`, `area:import-export`, `phase:1b-listings`, `priority:P1`, `complexity:M`
- **Depends on**: E5-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #3 (Duplicate detection)
- **Acceptance Criteria**:
  - [ ] Rows matching existing listings (by name+address or configured key) are flagged.
  - [ ] User can choose to skip, merge, or create duplicates during import review.
- **Definition of Done**: Common DoD + test dataset with intentional duplicates verified to be flagged correctly.

#### E5-3: Implement import progress tracking, error reporting, and rollback
- **Labels**: `type:feature`, `area:import-export`, `phase:1b-listings`, `priority:P0`, `complexity:L`
- **Depends on**: E5-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #3 (Progress tracking, Error reporting, Rollback capability); `docs/Product/02_MVP.md` §Performance Targets (10,000+ rows)
- **Acceptance Criteria**:
  - [ ] Import shows real-time progress status.
  - [ ] Row-level errors are reported in a downloadable/viewable log.
  - [ ] A completed import can be rolled back, removing/reverting the imported rows.
  - [ ] Import of 10,000+ rows completes reliably per `02_MVP.md` Success Criteria.
- **Definition of Done**: Common DoD + rollback verified to fully revert a test import with zero orphaned records.

#### E5-4: Implement CSV bulk export
- **Labels**: `type:feature`, `area:import-export`, `phase:1b-listings`, `priority:P1`, `complexity:S`
- **Depends on**: E4-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1B #4 Bulk Export; `docs/Architecture/API.md` §GET /listings/export`
- **Acceptance Criteria**:
  - [ ] Users can export all listings or a filtered subset to CSV.
  - [ ] Export is Excel-compatible.
  - [ ] Export includes an audit trail entry (timestamp, user, action).
- **Definition of Done**: Common DoD + exported file verified to open correctly in Excel/Google Sheets.

---

### Epic E6: AI Integration (Description, SEO, Content Quality)
*Milestone: M3 — Labels: `type:epic`, `area:ai`, `phase:1c-ai`*
*Source: `docs/Product/02_MVP.md` §Phase 1C; `docs/Architecture/API.md` §AI & Content*

#### E6-1: Integrate Claude API for single-listing description generation
- **Labels**: `type:feature`, `area:ai`, `phase:1c-ai`, `priority:P0`, `complexity:M`
- **Depends on**: E4-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1C #1; `docs/Architecture/API.md` §POST /ai/generate-description`
- **Acceptance Criteria**:
  - [ ] `POST /api/ai/generate-description` calls Claude with prompts based on category, existing description, and SEO keywords.
  - [ ] Generation completes in `<2s` per `02_MVP.md` performance targets.
  - [ ] Generated content requires human review before publishing (not auto-published).
- **Definition of Done**: Common DoD + generation latency measured and logged against the 2s target.

#### E6-2: Implement bulk AI description generation with rate limiting and version history
- **Labels**: `type:feature`, `area:ai`, `phase:1c-ai`, `priority:P1`, `complexity:L`
- **Depends on**: E6-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1C #1 (Bulk generation, Version history)
- **Acceptance Criteria**:
  - [ ] Users can trigger AI generation across multiple selected listings.
  - [ ] Requests are rate-limited to avoid API throttling/cost overrun.
  - [ ] Previous descriptions are retained and viewable as version history.
- **Definition of Done**: Common DoD + rate limiter verified to reject/queue requests beyond the configured threshold.

#### E6-3: Implement SEO analysis engine
- **Labels**: `type:feature`, `area:ai`, `phase:1c-ai`, `priority:P0`, `complexity:M`
- **Depends on**: E4-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1C #2; `docs/Architecture/API.md` §POST /ai/seo-analysis`
- **Acceptance Criteria**:
  - [ ] Analysis covers title length/keywords, meta description, keyword density, and Flesch-Kincaid readability score.
  - [ ] Output includes actionable suggestions, not only numeric scores.
  - [ ] Batch SEO audit can run across all organization listings.
- **Definition of Done**: Common DoD + output verified against a known-good and known-bad listing sample.

#### E6-4: Implement content quality scoring
- **Labels**: `type:feature`, `area:ai`, `phase:1c-ai`, `priority:P1`, `complexity:M`
- **Depends on**: E4-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1C #3; `docs/Architecture/API.md` §POST /ai/content-score`
- **Acceptance Criteria**:
  - [ ] Score reflects completeness (required fields filled), image quality (min dimensions/format), description length, and category appropriateness.
  - [ ] `POST /api/ai/content-score` returns a score and the contributing factors.
- **Definition of Done**: Common DoD + scoring logic unit-tested for each contributing factor independently.

---

### Epic E7: Analytics & Dashboard
*Milestone: M4 — Labels: `type:epic`, `area:analytics`, `phase:1d-analytics`*
*Source: `docs/Product/02_MVP.md` §Phase 1D; `docs/Architecture/API.md` §Analytics*

#### E7-1: Implement Analytics API endpoints
- **Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P0`, `complexity:L`
- **Depends on**: E2-8, E4-1
- **Source**: `docs/Architecture/API.md` §GET /analytics/dashboard, /analytics/listing/:id, /analytics/search-terms, /analytics/report`
- **Acceptance Criteria**:
  - [ ] All four documented endpoints implemented and return data matching `API.md` response shapes.
  - [ ] Endpoints are scoped to the caller's organization.
  - [ ] Dashboard endpoint responds in `<1s` per `02_MVP.md` performance targets.
- **Definition of Done**: Common DoD + response times measured against the 1s target with realistic seeded data.

#### E7-2: Build overview dashboard (key metrics, recent activity feed)
- **Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P0`, `complexity:M`
- **Depends on**: E7-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1D #1 Overview Dashboard
- **Acceptance Criteria**:
  - [ ] Displays total listings, total leads, average lead quality score, content completion %, and month-over-month trends.
  - [ ] Shows quick actions (create listing, import, view analytics).
  - [ ] Shows a recent activity feed of the last 10 changes.
- **Definition of Done**: Common DoD + dashboard loads in `<1s` with 1,000 seeded listings.

#### E7-3: Build listing analytics view
- **Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P0`, `complexity:M`
- **Depends on**: E7-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1D #2 Listing Analytics
- **Acceptance Criteria**:
  - [ ] Shows view count, click-through rate, lead capture count, time-to-first-lead, device breakdown, and traffic source per listing.
- **Definition of Done**: Common DoD + each metric verified against seeded `analytics_events` data.

#### E7-4: Build search analytics view
- **Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P1`, `complexity:M`
- **Depends on**: E7-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1D #3 Search Analytics
- **Acceptance Criteria**:
  - [ ] Shows search keywords leading to listings, average search position, impression count, and local/map view performance.
- **Definition of Done**: Common DoD + metrics verified against seeded `analytics_events` data.

#### E7-5: Build reports (monthly PDF, custom date ranges, comparisons, export)
- **Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P1`, `complexity:L`
- **Depends on**: E7-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1D #4 Reports
- **Acceptance Criteria**:
  - [ ] Monthly reports downloadable as PDF.
  - [ ] Custom date ranges selectable via date picker.
  - [ ] Month-over-month comparison view available.
  - [ ] Data exportable for further analysis.
- **Definition of Done**: Common DoD + generated PDF verified to render correctly and match on-screen data.

---

### Epic E8: Lead Capture & Management
*Milestone: M5 — Labels: `type:epic`, `area:leads`, `phase:1e-leads`*
*Source: `docs/Product/02_MVP.md` §Phase 1E; `docs/Architecture/API.md` §Leads*

#### E8-1: Implement Leads API endpoints
- **Labels**: `type:feature`, `area:leads`, `phase:1e-leads`, `priority:P0`, `complexity:M`
- **Depends on**: E2-7, E4-1
- **Source**: `docs/Architecture/API.md` §GET/POST/PATCH /leads, /leads/export`
- **Acceptance Criteria**:
  - [ ] `GET /api/leads` returns paginated leads scoped to the organization.
  - [ ] `POST /api/leads` captures a lead from a public form submission.
  - [ ] `PATCH /api/leads/:id` updates lead status.
  - [ ] `GET /api/leads/export` returns a CSV export.
- **Definition of Done**: Common DoD + endpoints match `API.md` contracts and are covered by integration tests.

#### E8-2: Build embedded lead capture form with spam protection
- **Labels**: `type:feature`, `area:leads`, `phase:1e-leads`, `priority:P0`, `complexity:M`
- **Depends on**: E8-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1E #1 Lead Capture Forms
- **Acceptance Criteria**:
  - [ ] Form embedded on listing pages with fields: name, email, phone, message, optional custom fields.
  - [ ] reCAPTCHA (or equivalent) blocks spam submissions.
  - [ ] Optional double opt-in supported.
- **Definition of Done**: Common DoD + spam-protection verified to reject a scripted/automated submission attempt.

#### E8-3: Build lead management list (status, quality score, export, bulk actions)
- **Labels**: `type:feature`, `area:leads`, `phase:1e-leads`, `priority:P0`, `complexity:M`
- **Depends on**: E8-1
- **Source**: `docs/Product/02_MVP.md` §Phase 1E #2 Lead Management
- **Acceptance Criteria**:
  - [ ] List shows all captured leads with status (new/contacted/converted/lost) and automated quality score.
  - [ ] Leads exportable to CSV.
  - [ ] Bulk actions available (mark as contacted, delete).
- **Definition of Done**: Common DoD + bulk action and export verified against a seeded set of 20+ leads.

#### E8-4: Implement lead notifications (email alert, in-app, digest)
- **Labels**: `type:feature`, `area:leads`, `phase:1e-leads`, `priority:P1`, `complexity:M`
- **Depends on**: E8-1, E1-2
- **Source**: `docs/Product/02_MVP.md` §Phase 1E #3 Notifications
- **Acceptance Criteria**:
  - [ ] New lead triggers an email alert via Resend.
  - [ ] In-app bell icon notification appears for new leads.
  - [ ] Daily digest email summarizes new leads.
- **Definition of Done**: Common DoD + all three notification channels verified to fire on a test lead submission.

---

## 6. Dependency Summary (Issue-Level Graph)

```
E1-1 → E1-2 → E1-3 → E1-4 → E1-5
              E1-2 → E2-1 → E2-2 → E2-3 → E2-4 → E2-5 → E2-6
                                                   E2-5 → E2-7
                                                   E2-5 → E2-8
       E2-3 → E3-1 → E3-2
              E3-1 → E3-3
              E3-1 → E3-4 → E3-5 (also needs E2-5)

E2-5 + E3-5 → E4-1 → E4-2 → E4-3
                    E4-1 + E2-4 → E4-4 → E4-5
                                  E4-4 → E4-6 (also needs E1-2)
                                  E4-4 → E4-7
                                  E4-4 → E4-8

E4-1 → E5-1 → E5-2
             E5-1 → E5-3
E4-1 → E5-4

E4-1 → E6-1 → E6-2
E4-1 → E6-3
E4-1 → E6-4

E2-8 + E4-1 → E7-1 → E7-2
                    E7-1 → E7-3
                    E7-1 → E7-4
                    E7-1 → E7-5

E2-7 + E4-1 → E8-1 → E8-2
                    E8-1 → E8-3
             E8-1 + E1-2 → E8-4
```

See `docs/Engineering/IMPLEMENTATION_ORDER.md` for the linear, single-developer build sequence derived from this graph.
