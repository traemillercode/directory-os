# DirectoryOS GitHub Issues — Complete Specification

**Source**: `docs/Engineering/MVP_BACKLOG.md`  
**Total Issues**: 43  
**Total Epics**: 8  
**Total Milestones**: 5  

This document contains the complete specification for all 43 GitHub issues. Copy the issue details below to create GitHub issues in your repository.

---

## MILESTONE 1: M1 — Core Infrastructure (Weeks 1-3)

**Description**: Foundational work — nothing else can start until M1 is complete. Includes deployment infrastructure, database foundation, and authentication/authorization.

**Issues**: 18 total  
**Epics Covered**: E1 (Infrastructure), E2 (Database), E3 (Authentication)  
**Exit Criteria**: Deployed app with auth, database, CI/CD pipeline working

---

### Epic E1: Core Infrastructure & Deployment (5 issues)

#### Issue E1-1: Scaffold Next.js + TypeScript + Tailwind + shadcn/ui project

**Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`  
**Milestone**: M1  
**Depends On**: None

**Description**:
We need a production-ready Next.js foundation with TypeScript, Tailwind CSS, and shadcn/ui components. This is the root dependency for all subsequent issues — nothing else can start until this is working.

**Acceptance Criteria**:
- [ ] Next.js 14+ with App Router
  - `npm run dev` starts the dev server on localhost:3000
  - `npm run build` completes without errors
  - `npm run start` runs the production build locally
- [ ] TypeScript Configuration
  - `tsconfig.json` is strict mode enabled
  - `npm run type-check` passes with zero errors
  - All component files are `.tsx`, all utilities are `.ts`
- [ ] Tailwind CSS
  - `tailwind.config.ts` is configured
  - Tailwind classes are available in components
  - `npm run build` includes Tailwind in the output
- [ ] shadcn/ui Setup
  - `components.json` exists with proper configuration
  - At least one shadcn component (e.g., Button) is installed and importable
  - Components can be rendered without errors
- [ ] Linting & Formatting
  - `.eslintrc.json` is configured for Next.js + TypeScript
  - `.prettierrc.json` is configured
  - `npm run lint` passes with zero errors
- [ ] Project Structure
  - `app/`, `components/`, `lib/`, `public/` directories exist
- [ ] GitHub Actions CI Ready
  - `npm run lint`, `npm run type-check`, `npm run test` commands exist

**Definition of Done** (from MVP_BACKLOG.md §5):
- [ ] Code implemented and matches ARCHITECTURE.md §Frontend Layer
- [ ] No new architecture/tables/endpoints introduced
- [ ] Unit/integration tests written and passing
- [ ] Linting and type-checking pass with no new errors
- [ ] No hardcoded secrets
- [ ] Code reviewed
- [ ] Relevant documentation updated
- [ ] Merged to main without breaking CI
- [ ] `npm run dev` starts cleanly on localhost:3000
- [ ] `npm run build` completes in < 30 seconds
- [ ] Project structure matches CODING_STANDARDS.md

**Source Documentation**: `docs/Architecture/ARCHITECTURE.md` §Frontend Layer

---

#### Issue E1-2: Provision Supabase project (Postgres, Auth, Storage)

**Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E1-1

**Description**:
Provision a Supabase project with PostgreSQL database, Auth service, and Storage buckets. This provides the backend infrastructure for all data and authentication.

**Acceptance Criteria**:
- [ ] Supabase project created for staging and production
- [ ] Postgres connection string available via environment variables
- [ ] Supabase Auth enabled (email/password method)
- [ ] Supabase Storage bucket created (e.g., `listing-images`)
- [ ] Local connection test: `psql $DATABASE_URL -c "SELECT 1;"` succeeds

**Definition of Done**:
- [ ] Connection verified from local app via a health-check script/route
- [ ] Environment variables configured correctly
- [ ] No secrets committed to source code
- [ ] Supabase CLI installed and verified working

**Source Documentation**: `docs/Architecture/ARCHITECTURE.md` §Backend Layer

---

#### Issue E1-3: Configure environment management (staging/production)

**Labels**: `type:chore`, `area:infra`, `phase:1a-core-infra`, `priority:P0`, `complexity:XS`  
**Milestone**: M1  
**Depends On**: E1-2

**Description**:
Set up environment variable management for staging and production, ensuring no secrets are committed to source control.

**Acceptance Criteria**:
- [ ] `.env.example` documents all required variables
- [ ] Staging and production environments have separate, non-shared secrets
- [ ] No secrets committed to source control
- [ ] GitHub secret scanning enabled and passing

**Definition of Done**:
- [ ] Secret scan run with zero findings
- [ ] `.env.local` is gitignored
- [ ] Vercel environment variables configured

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1A #3

---

#### Issue E1-4: Vercel deployment (staging + production) with GitHub Actions CI

**Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`  
**Milestone**: M1  
**Depends On**: E1-1, E1-3

**Description**:
Set up Vercel deployment for staging and production, with GitHub Actions CI pipeline that runs linting, type-checking, and tests on every PR.

**Acceptance Criteria**:
- [ ] Vercel project connected to repository with auto-deploy on merge to main
- [ ] Staging deployment triggers on PR/preview branches
- [ ] GitHub Actions workflow runs lint, type-check, and tests on every PR
- [ ] Failing CI blocks merge
- [ ] A test PR shows green CI and a working preview deployment URL

**Definition of Done**:
- [ ] `.github/workflows/ci.yml` exists and is valid
- [ ] Preview deployment URL works
- [ ] CI passes on test PR
- [ ] Branch protection rule requires passing CI

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1A #3; `docs/Product/01_PROJECT.md` §Technology Foundation

---

#### Issue E1-5: Integrate Sentry monitoring and logging

**Labels**: `type:infra`, `area:infra`, `phase:1a-core-infra`, `priority:P1`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E1-4

**Description**:
Integrate Sentry SDK for error tracking and monitoring in both frontend and API routes.

**Acceptance Criteria**:
- [ ] Sentry SDK integrated in frontend and API routes
- [ ] Errors from staging and production captured with environment tags
- [ ] A manually triggered test error appears in Sentry dashboard
- [ ] Source maps uploaded for stack trace accuracy
- [ ] Staging and production environments tagged separately

**Definition of Done**:
- [ ] Verified error captured in Sentry with source map support
- [ ] Sentry alerts configured
- [ ] Error grouping rules applied

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1A #3

---

### Epic E2: Database Foundation (8 issues)

#### Issue E2-1: Set up Prisma ORM and migration workflow

**Labels**: `type:infra`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E1-2

**Description**:
Set up Prisma ORM and establish the migration workflow for database schema management.

**Acceptance Criteria**:
- [ ] Prisma installed and connected to Supabase Postgres
- [ ] `prisma migrate` workflow documented and working locally and in CI
- [ ] A sample migration applies cleanly on a fresh database

**Definition of Done**:
- [ ] `prisma generate` auto-generates Prisma client types
- [ ] `prisma migrate dev --name init` creates first migration
- [ ] Migrations are version controlled

**Source Documentation**: `docs/Architecture/ARCHITECTURE.md` §Prisma ORM

---

#### Issue E2-2: Implement `organizations` table + RLS

**Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E2-1

**Description**:
Create the organizations table as the foundation for multi-tenancy, with Row-Level Security policies.

**Acceptance Criteria**:
- [ ] Table schema matches DATABASE.md (id, name, slug, timezone, industry, subscription_tier, etc.)
- [ ] Row-Level Security enabled scoping access by `organization_id`
- [ ] Indexes on `slug` and `subscription_tier` created
- [ ] RLS policy verified to block cross-organization reads in a test

**Definition of Done**:
- [ ] Migration applies cleanly
- [ ] RLS policies tested and verified
- [ ] Indexes created and verified

**Source Documentation**: `docs/Architecture/DATABASE.md` §1. organizations

---

#### Issue E2-3: Implement `users` table + RLS

**Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E2-2

**Description**:
Create the users table with role-based access control and multi-tenancy support.

**Acceptance Criteria**:
- [ ] Table schema matches DATABASE.md (id, organization_id FK, email, role, password_hash, etc.)
- [ ] Role check constraint enforces admin/editor/viewer
- [ ] RLS restricts users to their own organization
- [ ] Unique index on (organization_id, email)

**Definition of Done**:
- [ ] Migration applies cleanly
- [ ] Role check constraint verified to reject invalid roles
- [ ] RLS policies tested

**Source Documentation**: `docs/Architecture/DATABASE.md` §2. users

---

#### Issue E2-4: Implement `categories` table

**Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P1`, `complexity:XS`  
**Milestone**: M1  
**Depends On**: E2-2

**Description**:
Create the categories lookup table for listing classification.

**Acceptance Criteria**:
- [ ] Table stores category name, slug, hierarchy/classification
- [ ] Seed data for common categories available (e.g., "Plumber", "Electrician", "Restaurant")
- [ ] Seed script runs without error

**Definition of Done**:
- [ ] Migration applies cleanly
- [ ] Seed data persists and can be queried
- [ ] Foreign key relationships verified

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1A #2

---

#### Issue E2-5: Implement `listings` table + RLS

**Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`  
**Milestone**: M1  
**Depends On**: E2-2, E2-4

**Description**:
Create the core listings table with geospatial support and multi-tenancy enforcement.

**Acceptance Criteria**:
- [ ] Table includes all MVP fields: name, category, description, address, latitude/longitude, hours, images, social_links, status, etc.
- [ ] RLS scopes listings by `organization_id`
- [ ] Indexes support search/filter/sort at 1,000+ rows
- [ ] Geospatial indexes for location queries
- [ ] Soft delete support (deleted_at column)

**Definition of Done**:
- [ ] Query plan reviewed for primary listing search query
- [ ] Index usage verified via `EXPLAIN ANALYZE`
- [ ] RLS policies tested
- [ ] Geospatial queries tested

**Source Documentation**: `docs/Architecture/DATABASE.md` §3. listings

---

#### Issue E2-6: Implement `listings_audit` table

**Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P1`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E2-5

**Description**:
Create audit trail table for listing change tracking.

**Acceptance Criteria**:
- [ ] Table captures change tracking (who, what, when) for listing mutations
- [ ] Audit rows created automatically on listing create/update/delete
- [ ] A listing update produces exactly one corresponding audit row in tests

**Definition of Done**:
- [ ] Prisma schema includes trigger or application-level audit logic
- [ ] Migration applies cleanly
- [ ] Audit rows verified in integration tests

**Source Documentation**: `docs/Architecture/DATABASE.md` §6. listings_audit

---

#### Issue E2-7: Implement `leads` table

**Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P0`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E2-5

**Description**:
Create leads table for capturing form submissions.

**Acceptance Criteria**:
- [ ] Table includes: listing_id FK, name, email, phone, message, source, status, quality_score
- [ ] RLS scopes leads via the parent listing's organization
- [ ] Foreign key cascade behavior verified when a listing is deleted

**Definition of Done**:
- [ ] Migration applies cleanly
- [ ] FK cascade tested
- [ ] RLS policies verified

**Source Documentation**: `docs/Architecture/DATABASE.md` §4. leads

---

#### Issue E2-8: Implement `analytics_events` table

**Labels**: `type:feature`, `area:database`, `phase:1a-core-infra`, `priority:P1`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E2-5

**Description**:
Create analytics table for tracking user interactions and listing performance.

**Acceptance Criteria**:
- [ ] Table includes: organization_id, event_type (view/click/lead), listing_id, metadata JSON, timestamp
- [ ] Table can sustain write volume needed for dashboard analytics
- [ ] Indexes on organization_id, listing_id, timestamp
- [ ] Insert/read benchmarked against performance targets

**Definition of Done**:
- [ ] Migration applies cleanly
- [ ] Indexes created and verified
- [ ] Performance benchmarked

**Source Documentation**: `docs/Architecture/DATABASE.md` §5. analytics_events

---

### Epic E3: Authentication & Authorization (5 issues)

#### Issue E3-1: Implement signup/login/logout API endpoints

**Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`  
**Milestone**: M1  
**Depends On**: E2-3, E1-2

**Description**:
Implement authentication endpoints for user signup, login, and logout.

**Acceptance Criteria**:
- [ ] `POST /api/auth/signup` creates a user + organization and returns access/refresh tokens
- [ ] `POST /api/auth/login` authenticates and returns tokens
- [ ] `POST /api/auth/logout` invalidates the session/token
- [ ] Response shape matches API.md contracts exactly
- [ ] Passwords are hashed; plaintext passwords never stored or logged

**Definition of Done**:
- [ ] Endpoints match request/response contracts in API.md exactly
- [ ] Integration tests cover happy path, validation failures, auth failures
- [ ] Passwords verified hashed (bcrypt)

**Source Documentation**: `docs/Architecture/API.md` §Authentication Endpoints

---

#### Issue E3-2: Implement JWT session management with refresh/token rotation

**Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`  
**Milestone**: M1  
**Depends On**: E3-1

**Description**:
Implement JWT token refresh and rotation for secure session management.

**Acceptance Criteria**:
- [ ] `POST /api/auth/refresh` issues a new access token from a valid refresh token
- [ ] Expired/invalid tokens are rejected with correct status codes
- [ ] Token rotation invalidates the previous refresh token
- [ ] Rotation behavior covered by automated test

**Definition of Done**:
- [ ] Integration tests verify token refresh flow
- [ ] Token expiry times configurable via env vars
- [ ] httpOnly cookies used for refresh tokens

**Source Documentation**: `docs/Architecture/API.md` §POST /auth/refresh

---

#### Issue E3-3: Implement password reset flow via Resend

**Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P1`, `complexity:S`  
**Milestone**: M1  
**Depends On**: E3-1

**Description**:
Implement password reset flow using Resend for email delivery.

**Acceptance Criteria**:
- [ ] Requesting a reset sends an email via Resend with a time-limited reset link/token
- [ ] Submitting a valid token updates the password
- [ ] Expired/used tokens are rejected
- [ ] End-to-end reset flow tested with a test email account

**Definition of Done**:
- [ ] Resend integration tested
- [ ] Token expiry times verified (e.g., 1 hour)
- [ ] Email template renders correctly

**Source Documentation**: `docs/Architecture/API.md` §POST /auth/password-reset

---

#### Issue E3-4: Implement RBAC middleware (admin/editor/viewer)

**Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`  
**Milestone**: M1  
**Depends On**: E3-1, E2-3

**Description**:
Implement role-based access control middleware to enforce permissions across the API.

**Acceptance Criteria**:
- [ ] Admin has full access to all organization resources
- [ ] Editor can manage listings and view analytics but not manage users/billing
- [ ] Viewer has read-only access
- [ ] Unauthorized actions return 403 with a clear error
- [ ] Each role tested against at least one allowed and one disallowed action

**Definition of Done**:
- [ ] RBAC middleware applies to all protected routes
- [ ] Role checks verified in integration tests
- [ ] Permission denied responses standardized

**Source Documentation**: `docs/Product/02_MVP.md` §Role-based access control (RBAC)

---

#### Issue E3-5: Implement organization multi-tenancy scoping across API

**Labels**: `type:feature`, `area:auth`, `phase:1a-core-infra`, `priority:P0`, `complexity:M`  
**Milestone**: M1  
**Depends On**: E3-4, E2-5

**Description**:
Implement defense-in-depth tenant isolation across the API layer.

**Acceptance Criteria**:
- [ ] Every authenticated request resolves the caller's `organization_id`
- [ ] All data queries are scoped to the caller's organization
- [ ] Cross-organization data access attempts are blocked and logged
- [ ] A test proves user A cannot read/write user B's organization data

**Definition of Done**:
- [ ] Organization scoping enforced at every API route
- [ ] Cross-org access blocked by both API and RLS policies
- [ ] Integration tests verify isolation

**Source Documentation**: `docs/Product/02_MVP.md` §Organization multi-tenancy

---

## MILESTONE 2: M2 — Listing Management (Weeks 4-6)

**Description**: Listing CRUD operations, dashboard, and bulk import/export.

**Issues**: 12 total  
**Epics Covered**: E4 (Listing CRUD & Dashboard), E5 (Bulk Import/Export)  
**Exit Criteria**: Users can CRUD, search, filter, bulk import/export 1,000+ listings  
**Depends On**: M1 complete

---

### Epic E4: Listing Management (8 issues)

#### Issue E4-1: Implement Listings CRUD API endpoints

**Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P0`, `complexity:L`  
**Milestone**: M2  
**Depends On**: E2-5, E3-5

**Description**:
Implement complete CRUD API for listing management.

**Acceptance Criteria**:
- [ ] `GET /api/listings` supports pagination and filtering (status, category, search)
- [ ] `POST /api/listings` creates a listing with validation on required fields
- [ ] `GET /api/listings/:id` returns a single listing
- [ ] `PATCH /api/listings/:id` updates a listing and writes an audit row
- [ ] `DELETE /api/listings/:id` performs a soft delete (status change, not row removal)
- [ ] All five endpoints covered by integration tests
- [ ] All endpoints match API.md contracts

**Definition of Done**:
- [ ] All five endpoints pass integration tests
- [ ] Pagination verified with 1,000+ listings
- [ ] Audit rows created on mutations
- [ ] RLS verified to block cross-org access

**Source Documentation**: `docs/Architecture/API.md` §Listings

---

#### Issue E4-2: Build listing dashboard table view (search, filter, sort, pagination)

**Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P0`, `complexity:L`  
**Milestone**: M2  
**Depends On**: E4-1

**Description**:
Build the listing dashboard UI with search, filter, sort, and pagination.

**Acceptance Criteria**:
- [ ] Table displays all organization listings with search by name/category/status
- [ ] Sorting by date, name, and lead count works
- [ ] Pagination performs correctly at 1,000+ listings without UI slowdown
- [ ] Search response < 500ms per performance targets
- [ ] Quick stats (total, active, pending, archived) shown
- [ ] Manual/automated test confirms search response under 500ms with 1,000 listings

**Definition of Done**:
- [ ] Dashboard loads in < 1s
- [ ] Search/filter/sort all working
- [ ] Pagination tested at 1,000+ listings
- [ ] Responsive design verified

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #1

---

#### Issue E4-3: Implement bulk actions on listing dashboard

**Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P1`, `complexity:M`  
**Milestone**: M2  
**Depends On**: E4-2

**Description**:
Add bulk operations (status change, delete) to the listing dashboard.

**Acceptance Criteria**:
- [ ] Users can select multiple listings via checkboxes
- [ ] Bulk operations (status change, delete) are available
- [ ] Bulk actions respect RBAC (Viewer cannot perform them)
- [ ] Bulk action tested against 10+ selected rows

**Definition of Done**:
- [ ] Bulk actions working for status change and delete
- [ ] RBAC verified to block Viewer from bulk actions
- [ ] Integration tests cover bulk operations

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #1

---

#### Issue E4-4: Build create/edit listing form with validation

**Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P0`, `complexity:L`  
**Milestone**: M2  
**Depends On**: E4-1, E2-4

**Description**:
Build the listing creation and editing form with comprehensive validation.

**Acceptance Criteria**:
- [ ] Form includes all MVP fields: name, category (multi-select), description, phone, email, website, address, hours, images, social links
- [ ] Client and server-side validation enforce required fields and formats
- [ ] Rich text editor available for description field
- [ ] Invalid submissions blocked with field-level error messages

**Definition of Done**:
- [ ] Form validation working client and server-side
- [ ] Error messages clear and actionable
- [ ] Rich text editor rendering correctly
- [ ] Form accessible (WCAG AA)

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #2

---

#### Issue E4-5: Implement Mapbox address autocomplete

**Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P1`, `complexity:M`  
**Milestone**: M2  
**Depends On**: E4-4

**Description**:
Integrate Mapbox address autocomplete for easy location entry.

**Acceptance Criteria**:
- [ ] Address field suggests results via Mapbox as the user types
- [ ] Selecting a suggestion populates address, latitude, and longitude
- [ ] Manual address entry remains possible if Mapbox is unavailable
- [ ] Latitude/longitude verified to persist correctly

**Definition of Done**:
- [ ] Mapbox integration tested
- [ ] Geolocation data persisted and retrievable
- [ ] Fallback for manual entry working

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #2

---

#### Issue E4-6: Implement image upload gallery via Supabase Storage

**Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P1`, `complexity:M`  
**Milestone**: M2  
**Depends On**: E4-4, E1-2

**Description**:
Implement image upload and gallery for listings.

**Acceptance Criteria**:
- [ ] Users can upload multiple images per listing
- [ ] Images can be reordered via drag-to-reorder
- [ ] Images are stored in Supabase Storage and referenced by URL
- [ ] Upload size/type validated and rejected gracefully when invalid

**Definition of Done**:
- [ ] Image upload working end-to-end
- [ ] Images persistent in Supabase Storage
- [ ] Drag-to-reorder functioning
- [ ] File validation preventing uploads of wrong type/size

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #2

---

#### Issue E4-7: Implement auto-save draft protection

**Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P2`, `complexity:S`  
**Milestone**: M2  
**Depends On**: E4-4

**Description**:
Implement auto-save functionality to protect form drafts.

**Acceptance Criteria**:
- [ ] Form changes are periodically saved as a draft without explicit user action
- [ ] Reloading the page recovers the last auto-saved draft
- [ ] Draft recovery verified after a simulated page reload

**Definition of Done**:
- [ ] Auto-save working without blocking UI
- [ ] Draft recovery tested
- [ ] Stale drafts handled appropriately

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #2

---

#### Issue E4-8: Implement listing preview mode

**Labels**: `type:feature`, `area:listings`, `phase:1b-listings`, `priority:P2`, `complexity:S`  
**Milestone**: M2  
**Depends On**: E4-4

**Description**:
Implement preview mode to show how listings will appear before publishing.

**Acceptance Criteria**:
- [ ] Users can preview how a listing will appear before publishing
- [ ] Preview reflects current unsaved form state
- [ ] Preview verified to match published output

**Definition of Done**:
- [ ] Preview rendering correctly
- [ ] Preview reflecting unsaved changes
- [ ] Published output matches preview

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #2

---

### Epic E5: Bulk Import & Export (4 issues)

#### Issue E5-1: Implement CSV bulk import upload + mapping wizard

**Labels**: `type:feature`, `area:import-export`, `phase:1b-listings`, `priority:P0`, `complexity:L`  
**Milestone**: M2  
**Depends On**: E4-1

**Description**:
Implement CSV bulk import with column mapping and validation.

**Acceptance Criteria**:
- [ ] Users can upload a CSV file
- [ ] A mapping wizard matches CSV columns to listing fields, with auto-detection
- [ ] Uploaded data is validated before import (required fields, formats)
- [ ] A sample CSV of 100 rows imports correctly end-to-end

**Definition of Done**:
- [ ] Import flow working end-to-end
- [ ] Column mapping wizard functional
- [ ] Validation catching errors
- [ ] Test CSV imported successfully

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #3

---

#### Issue E5-2: Implement duplicate detection during import

**Labels**: `type:feature`, `area:import-export`, `phase:1b-listings`, `priority:P1`, `complexity:M`  
**Milestone**: M2  
**Depends On**: E5-1

**Description**:
Implement duplicate detection to prevent duplicate listing creation during imports.

**Acceptance Criteria**:
- [ ] Rows matching existing listings (by name+address or configured key) are flagged
- [ ] User can choose to skip, merge, or create duplicates during import review
- [ ] Test dataset with intentional duplicates verified to be flagged correctly

**Definition of Done**:
- [ ] Duplicate detection working
- [ ] User options for handling duplicates functional
- [ ] Integration tests cover duplicate scenarios

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #3

---

#### Issue E5-3: Implement import progress tracking, error reporting, and rollback

**Labels**: `type:feature`, `area:import-export`, `phase:1b-listings`, `priority:P0`, `complexity:L`  
**Milestone**: M2  
**Depends On**: E5-1

**Description**:
Implement real-time progress tracking, error reporting, and rollback capability for imports.

**Acceptance Criteria**:
- [ ] Import shows real-time progress status
- [ ] Row-level errors are reported in a downloadable/viewable log
- [ ] A completed import can be rolled back, removing/reverting the imported rows
- [ ] Import of 10,000+ rows completes reliably per Success Criteria
- [ ] Rollback fully reverts a test import with zero orphaned records

**Definition of Done**:
- [ ] Progress tracking showing real-time updates
- [ ] Error log downloadable and clear
- [ ] Rollback tested and verified complete
- [ ] 10,000+ row import tested

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #3

---

#### Issue E5-4: Implement CSV bulk export

**Labels**: `type:feature`, `area:import-export`, `phase:1b-listings`, `priority:P1`, `complexity:S`  
**Milestone**: M2  
**Depends On**: E4-1

**Description**:
Implement CSV export of listings with audit trail.

**Acceptance Criteria**:
- [ ] Users can export all listings or a filtered subset to CSV
- [ ] Export is Excel-compatible
- [ ] Export includes an audit trail entry (timestamp, user, action)
- [ ] Exported file verified to open correctly in Excel/Google Sheets

**Definition of Done**:
- [ ] Export functionality working
- [ ] CSV format correct and Excel-compatible
- [ ] Audit trail entry created

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1B #4

---

## MILESTONE 3: M3 — AI Features (Weeks 7-9)

**Description**: AI-powered description generation, SEO analysis, and content scoring.

**Issues**: 4 total  
**Epics Covered**: E6 (AI Integration)  
**Exit Criteria**: AI endpoints working, generation <2s, human review enforced  
**Depends On**: M2 complete

---

### Epic E6: AI Integration (4 issues)

#### Issue E6-1: Integrate Claude API for single-listing description generation

**Labels**: `type:feature`, `area:ai`, `phase:1c-ai`, `priority:P0`, `complexity:M`  
**Milestone**: M3  
**Depends On**: E4-1

**Description**:
Integrate Claude API for AI-powered listing description generation.

**Acceptance Criteria**:
- [ ] `POST /api/ai/generate-description` calls Claude with prompts based on category, existing description, and SEO keywords
- [ ] Generation completes in < 2s per performance targets
- [ ] Generated content requires human review before publishing (not auto-published)
- [ ] Generation latency measured and logged against the 2s target

**Definition of Done**:
- [ ] Claude API integrated
- [ ] Performance benchmarked
- [ ] Human review workflow enforced
- [ ] Integration tests cover happy path and error cases

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1C #1

---

#### Issue E6-2: Implement bulk AI description generation with rate limiting and version history

**Labels**: `type:feature`, `area:ai`, `phase:1c-ai`, `priority:P1`, `complexity:L`  
**Milestone**: M3  
**Depends On**: E6-1

**Description**:
Implement bulk generation with rate limiting and version history.

**Acceptance Criteria**:
- [ ] Users can trigger AI generation across multiple selected listings
- [ ] Requests are rate-limited to avoid API throttling/cost overrun
- [ ] Previous descriptions are retained and viewable as version history
- [ ] Rate limiter verified to reject/queue requests beyond threshold

**Definition of Done**:
- [ ] Bulk generation working
- [ ] Rate limiting enforced
- [ ] Version history persisting
- [ ] Integration tests cover bulk scenarios

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1C #1

---

#### Issue E6-3: Implement SEO analysis engine

**Labels**: `type:feature`, `area:ai`, `phase:1c-ai`, `priority:P0`, `complexity:M`  
**Milestone**: M3  
**Depends On**: E4-1

**Description**:
Implement SEO analysis covering title, keywords, descriptions, and readability.

**Acceptance Criteria**:
- [ ] Analysis covers title length/keywords, meta description, keyword density, and Flesch-Kincaid readability score
- [ ] Output includes actionable suggestions, not only numeric scores
- [ ] Batch SEO audit can run across all organization listings
- [ ] Output verified against known-good and known-bad listing samples

**Definition of Done**:
- [ ] SEO analysis working
- [ ] Actionable suggestions generated
- [ ] Batch audit functional
- [ ] Integration tests cover various content scenarios

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1C #2

---

#### Issue E6-4: Implement content quality scoring

**Labels**: `type:feature`, `area:ai`, `phase:1c-ai`, `priority:P1`, `complexity:M`  
**Milestone**: M3  
**Depends On**: E4-1

**Description**:
Implement content completeness and quality scoring.

**Acceptance Criteria**:
- [ ] Score reflects completeness (required fields filled), image quality (min dimensions/format), description length, and category appropriateness
- [ ] `POST /api/ai/content-score` returns a score and the contributing factors
- [ ] Scoring logic unit-tested for each contributing factor independently

**Definition of Done**:
- [ ] Content scoring working
- [ ] Factors breakdown clear
- [ ] Unit tests cover each factor
- [ ] Integration tests verify end-to-end scoring

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1C #3

---

## MILESTONE 4: M4 — Analytics (Weeks 10-11)

**Description**: Analytics API and dashboards.

**Issues**: 5 total  
**Epics Covered**: E7 (Analytics & Reporting)  
**Exit Criteria**: Dashboards load <1s, metrics display real data  
**Depends On**: M1 (Database), M2 (Listing data)

---

### Epic E7: Analytics & Dashboard (5 issues)

#### Issue E7-1: Implement Analytics API endpoints

**Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P0`, `complexity:L`  
**Milestone**: M4  
**Depends On**: E2-8, E4-1

**Description**:
Implement all documented analytics API endpoints.

**Acceptance Criteria**:
- [ ] All four documented endpoints implemented and return data matching API.md shapes
  - GET /analytics/dashboard
  - GET /analytics/listing/:id
  - GET /analytics/search-terms
  - GET /analytics/report
- [ ] Endpoints are scoped to the caller's organization
- [ ] Dashboard endpoint responds in < 1s per performance targets
- [ ] Response times measured against 1s target with realistic seeded data

**Definition of Done**:
- [ ] All four endpoints implemented
- [ ] Performance benchmarked
- [ ] Integration tests cover all endpoints
- [ ] RLS verified to block cross-org access

**Source Documentation**: `docs/Architecture/API.md` §Analytics

---

#### Issue E7-2: Build overview dashboard (key metrics, recent activity feed)

**Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P0`, `complexity:M`  
**Milestone**: M4  
**Depends On**: E7-1

**Description**:
Build the overview dashboard with key metrics and activity feed.

**Acceptance Criteria**:
- [ ] Displays total listings, total leads, average lead quality score, content completion %, and month-over-month trends
- [ ] Shows quick actions (create listing, import, view analytics)
- [ ] Shows a recent activity feed of the last 10 changes
- [ ] Dashboard loads in < 1s with 1,000 seeded listings

**Definition of Done**:
- [ ] Dashboard rendering correctly
- [ ] Metrics accurate and real-time
- [ ] Activity feed showing correct data
- [ ] Performance verified < 1s

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1D #1

---

#### Issue E7-3: Build listing analytics view

**Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P0`, `complexity:M`  
**Milestone**: M4  
**Depends On**: E7-1

**Description**:
Build per-listing analytics dashboard.

**Acceptance Criteria**:
- [ ] Shows view count, click-through rate, lead capture count, time-to-first-lead, device breakdown, and traffic source per listing
- [ ] Each metric verified against seeded analytics_events data

**Definition of Done**:
- [ ] Listing analytics view working
- [ ] Metrics accurate
- [ ] Integration tests verify metrics

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1D #2

---

#### Issue E7-4: Build search analytics view

**Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P1`, `complexity:M`  
**Milestone**: M4  
**Depends On**: E7-1

**Description**:
Build search keyword and performance analytics.

**Acceptance Criteria**:
- [ ] Shows search keywords leading to listings, average search position, impression count, and local/map view performance
- [ ] Metrics verified against seeded analytics_events data

**Definition of Done**:
- [ ] Search analytics view working
- [ ] Metrics accurate
- [ ] Integration tests verify metrics

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1D #3

---

#### Issue E7-5: Build reports (monthly PDF, custom date ranges, comparisons, export)

**Labels**: `type:feature`, `area:analytics`, `phase:1d-analytics`, `priority:P1`, `complexity:L`  
**Milestone**: M4  
**Depends On**: E7-1

**Description**:
Build comprehensive reporting with PDF generation.

**Acceptance Criteria**:
- [ ] Monthly reports downloadable as PDF
- [ ] Custom date ranges selectable via date picker
- [ ] Month-over-month comparison view available
- [ ] Data exportable for further analysis
- [ ] Generated PDF verified to render correctly and match on-screen data

**Definition of Done**:
- [ ] Report generation working
- [ ] PDF rendering correctly
- [ ] Custom date ranges functional
- [ ] Comparisons accurate

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1D #4

---

## MILESTONE 5: M5 — Lead Management (Weeks 11-12)

**Description**: Lead capture, management, and notifications.

**Issues**: 4 total  
**Epics Covered**: E8 (Lead Capture & Management)  
**Exit Criteria**: Leads captured, managed, exported; notifications working  
**Depends On**: M1 (Auth), M2 (Listings), M4 (Analytics)

---

### Epic E8: Lead Capture & Management (4 issues)

#### Issue E8-1: Implement Leads API endpoints

**Labels**: `type:feature`, `area:leads`, `phase:1e-leads`, `priority:P0`, `complexity:M`  
**Milestone**: M5  
**Depends On**: E2-7, E4-1

**Description**:
Implement all lead management API endpoints.

**Acceptance Criteria**:
- [ ] `GET /api/leads` returns paginated leads scoped to the organization
- [ ] `POST /api/leads` captures a lead from a public form submission
- [ ] `PATCH /api/leads/:id` updates lead status
- [ ] `GET /api/leads/export` returns a CSV export
- [ ] Endpoints match API.md contracts and are covered by integration tests

**Definition of Done**:
- [ ] All four endpoints implemented
- [ ] Integration tests cover all endpoints
- [ ] RLS verified to block cross-org access
- [ ] Error handling standardized

**Source Documentation**: `docs/Architecture/API.md` §Leads

---

#### Issue E8-2: Build embedded lead capture form with spam protection

**Labels**: `type:feature`, `area:leads`, `phase:1e-leads`, `priority:P0`, `complexity:M`  
**Milestone**: M5  
**Depends On**: E8-1

**Description**:
Build embedded lead capture form with spam protection.

**Acceptance Criteria**:
- [ ] Form embedded on listing pages with fields: name, email, phone, message, optional custom fields
- [ ] reCAPTCHA (or equivalent) blocks spam submissions
- [ ] Optional double opt-in supported
- [ ] Spam-protection verified to reject a scripted/automated submission attempt

**Definition of Done**:
- [ ] Form rendering correctly
- [ ] Spam protection working
- [ ] Form submissions captured
- [ ] Integration tests cover form submission

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1E #1

---

#### Issue E8-3: Build lead management list (status, quality score, export, bulk actions)

**Labels**: `type:feature`, `area:leads`, `phase:1e-leads`, `priority:P0`, `complexity:M`  
**Milestone**: M5  
**Depends On**: E8-1

**Description**:
Build lead management interface.

**Acceptance Criteria**:
- [ ] List shows all captured leads with status (new/contacted/converted/lost) and automated quality score
- [ ] Leads exportable to CSV
- [ ] Bulk actions available (mark as contacted, delete)
- [ ] Bulk action and export verified against a seeded set of 20+ leads

**Definition of Done**:
- [ ] Lead list rendering correctly
- [ ] Status updates working
- [ ] Export functional
- [ ] Bulk actions tested

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1E #2

---

#### Issue E8-4: Implement lead notifications (email alert, in-app, digest)

**Labels**: `type:feature`, `area:leads`, `phase:1e-leads`, `priority:P1`, `complexity:M`  
**Milestone**: M5  
**Depends On**: E8-1, E1-2

**Description**:
Implement multi-channel lead notifications.

**Acceptance Criteria**:
- [ ] New lead triggers an email alert via Resend
- [ ] In-app bell icon notification appears for new leads
- [ ] Daily digest email summarizes new leads
- [ ] All three notification channels verified to fire on a test lead submission

**Definition of Done**:
- [ ] Email notifications working
- [ ] In-app notifications displaying
- [ ] Digest emails generating
- [ ] Integration tests cover all notification channels

**Source Documentation**: `docs/Product/02_MVP.md` §Phase 1E #3

---

## Summary

**Total Issues**: 43  
**Total Milestones**: 5  
**Total Epics**: 8  
**Expected Duration**: 12 weeks (3 months)  
**Team Size**: 1 developer (solo)

**Critical Path**: E1-1 → E1-2 → E2-1 → E2-2 → E2-3 → E2-5 → E3-1 → E4-1 (and all downstream)

See `docs/Engineering/IMPLEMENTATION_ORDER.md` for the recommended build sequence.
