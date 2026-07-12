# DirectoryOS MVP Specification

## Overview

The MVP (Minimum Viable Product) focuses on delivering core directory management and listing automation capabilities. This phased approach allows us to validate product-market fit while maintaining architectural integrity for future scaling.

**Duration**: 12 weeks
**Target Launch**: Production-ready SaaS platform
**Success Metric**: Teams can manage 1,000+ listings with 80% less manual effort

## MVP Features

### Phase 1A: Core Infrastructure (Weeks 1-3)

#### 1. Authentication & Authorization
- **Email/password signup and login**
- **Organization multi-tenancy** (one account → multiple team members)
- **Role-based access control** (RBAC):
  - Admin (full access)
  - Editor (manage listings, view analytics)
  - Viewer (read-only access)
- **Session management** (JWT tokens via Supabase)
- **Account recovery** (password reset via Resend email)
- **Google OAuth** (future phase)

#### 2. Database Foundation
- **Organizations** table (workspace isolation)
- **Users** table (authentication & authorization)
- **Listings** table (core business directory data)
- **Categories** table (listing classification)
- **Listings_Audit** table (change tracking)
- **Analytics_Events** table (user behavior tracking)

#### 3. Deployment & Infrastructure
- **Vercel deployment** (zero-config Next.js hosting)
- **Supabase PostgreSQL** (managed database)
- **Environment configuration** (staging, production)
- **GitHub Actions** (automated testing & deployment)
- **Monitoring & logging** (Sentry integration)

### Phase 1B: Listing Management (Weeks 4-6)

#### 1. Listing Dashboard
- **Table view** of all organization listings
- **Search & filtering** (by category, status, name)
- **Sorting** (by date, name, lead count)
- **Pagination** (1,000+ listings without slowdown)
- **Bulk actions** (select multiple, perform operations)
- **Quick stats** (total listings, active, pending, archived)

#### 2. Create & Edit Listings
- **Form with validation** for core listing fields:
  - Business name
  - Category (multi-select)
  - Description
  - Phone
  - Email
  - Website
  - Address (with Mapbox autocomplete)
  - Hours of operation
  - Images (gallery with drag-to-reorder)
  - Social media links
- **Auto-save** (draft protection)
- **Rich text editor** for descriptions
- **Image upload** (via Supabase Storage)
- **Preview mode** (see how listing appears)

#### 3. Bulk Import
- **CSV upload** with validation
- **Mapping wizard** (match CSV columns to fields)
- **Duplicate detection** (prevent double-entry)
- **Progress tracking** (real-time status)
- **Error reporting** (detailed import logs)
- **Rollback capability** (undo failed imports)

#### 4. Bulk Export
- **Export to CSV** (all listings or filtered subset)
- **Excel-compatible** formatting
- **Audit trail** (timestamp, user, action)

### Phase 1C: AI Integration (Weeks 7-9)

#### 1. AI Description Generation
- **Claude API integration** (via Anthropic)
- **Smart prompts** based on:
  - Business category
  - Existing description
  - Industry best practices
  - SEO keywords
- **One-click generation** for single listings
- **Bulk generation** with rate limiting
- **Version history** (keep previous descriptions)
- **Human review** before publishing

#### 2. SEO Analysis
- **Title optimization** (length, keywords)
- **Meta description** check
- **Keyword density** analysis
- **Readability score** (Flesch-Kincaid)
- **Actionable suggestions** (not just scores)
- **Batch SEO audit** (all listings)

#### 3. Content Quality Scoring
- **Completeness check** (required fields filled)
- **Image quality** (minimum dimensions, format)
- **Description length** (optimal range)
- **Category appropriateness** (smart detection)

### Phase 1D: Analytics & Dashboard (Weeks 10-11)

#### 1. Overview Dashboard
- **Key metrics**:
  - Total listings
  - Total leads captured
  - Average lead quality score
  - Content completion %
  - This month vs. last month (trends)
- **Quick actions** (create listing, import, view analytics)
- **Recent activity feed** (last 10 changes)

#### 2. Listing Analytics
- **View count** (how many times listing was viewed)
- **Click-through rate** (clicks to website)
- **Lead capture** (form submissions)
- **Time to first lead** (how fast leads came)
- **Device breakdown** (mobile vs. desktop)
- **Traffic source** (direct, search, maps, etc.)

#### 3. Search Analytics
- **Search keywords** that led to listings
- **Search position** tracking (avg rank)
- **Impression count** (search results shown)
- **Local search performance** (map views)

#### 4. Reports
- **Monthly reports** (PDF downloadable)
- **Custom date ranges** (date picker)
- **Compare periods** (month-over-month)
- **Export data** (for further analysis)

### Phase 1E: Lead Capture (Weeks 11-12)

#### 1. Lead Capture Forms
- **Embedded contact form** (on listing pages)
- **Form fields**:
  - Name
  - Email
  - Phone
  - Message
  - Custom fields (optional)
- **SPAM protection** (reCAPTCHA)
- **Double opt-in** (optional)

#### 2. Lead Management
- **Lead list** (all captured leads)
- **Lead status** (new, contacted, converted, lost)
- **Lead quality score** (automated scoring)
- **Lead export** (CSV, Salesforce integration in future)
- **Bulk actions** (mark as contacted, delete)

#### 3. Notifications
- **Email alert** when new lead arrives
- **In-app notifications** (bell icon)
- **Digest emails** (daily summary)

## MVP Non-Features (Out of Scope)

❌ Social media posting
❌ Email automation sequences
❌ Paid advertising management
❌ Event calendar integration
❌ White-label customization
❌ Custom domain support
❌ API for third-party integrations
❌ Mobile native apps

## User Flows

### New User Onboarding
1. Sign up → Email verification → Create organization
2. Set up timezone, industry
3. Either: Create first listing OR Import CSV
4. Explore dashboard, view analytics template
5. Claim free trial (14 days)

### Listing Management Workflow
1. Dashboard → View all listings
2. Search/filter to find target listings
3. Edit listing (inline or full form)
4. AI-assist to improve description
5. Review SEO score
6. Publish changes
7. Monitor leads & analytics

### Import Workflow
1. Dashboard → Bulk Import
2. Upload CSV
3. Map columns (auto-detect when possible)
4. Preview data
5. Confirm import
6. Monitor progress
7. Review error report (if any)

## Technical Specifications

### Database Schema (MVP Subset)
```
organizations
├── id (UUID)
├── name
├── timezone
├── industry
├── subscription_tier
├── created_at
├── updated_at

users
├── id (UUID)
├── organization_id (FK)
├── email
├── full_name
├── role (admin|editor|viewer)
├── password_hash
├── email_verified
├── created_at

listings
├── id (UUID)
├── organization_id (FK)
├── name
├── category (array)
├── description
├── short_description
├── phone
├── email
├── website
├── address
├── latitude
├── longitude
├── hours_json
├── images (array of URLs)
├── social_links (JSON)
├── status (draft|published|archived)
├── created_at
├── updated_at

leads
├── id (UUID)
├── listing_id (FK)
├── name
├── email
├── phone
├── message
├── source (form|email|phone)
├── status (new|contacted|converted|lost)
├── quality_score
├── created_at

analytics_events
├── id (UUID)
├── organization_id (FK)
├── event_type (view|click|lead)
├── listing_id (FK)
├── metadata (JSON)
├── timestamp
```

### API Endpoints (MVP)

**Authentication**
- `POST /api/auth/signup`
- `POST /api/auth/login`
- `POST /api/auth/logout`
- `POST /api/auth/refresh`
- `POST /api/auth/password-reset`

**Listings**
- `GET /api/listings` (paginated, filterable)
- `POST /api/listings` (create)
- `GET /api/listings/:id` (get single)
- `PATCH /api/listings/:id` (update)
- `DELETE /api/listings/:id` (soft delete)
- `POST /api/listings/:id/bulk-import` (import CSV)
- `GET /api/listings/export` (CSV export)

**AI & Content**
- `POST /api/ai/generate-description`
- `POST /api/ai/seo-analysis`
- `POST /api/ai/content-score`

**Analytics**
- `GET /api/analytics/dashboard`
- `GET /api/analytics/listing/:id`
- `GET /api/analytics/search-terms`
- `GET /api/analytics/report`

**Leads**
- `GET /api/leads` (paginated)
- `POST /api/leads` (capture from form)
- `PATCH /api/leads/:id` (update status)
- `GET /api/leads/export`

### Performance Targets

| Metric | Target |
|--------|--------|
| Dashboard load | <1s |
| Search 1,000 listings | <500ms |
| AI description generation | <2s |
| Form submit | <1s |
| CSV import (1,000 rows) | <30s |
| Analytics load | <1s |

### Security Requirements

- ✅ HTTPS only
- ✅ JWT token rotation
- ✅ CORS properly configured
- ✅ SQL injection prevention (Prisma ORM)
- ✅ XSS protection (Next.js default)
- ✅ CSRF tokens on forms
- ✅ Rate limiting on API endpoints
- ✅ Input validation on all forms
- ✅ Audit logging of sensitive actions
- ✅ Data encryption at rest (Supabase)

### Compliance

- ✅ GDPR ready (data export, deletion)
- ✅ CCPA ready (privacy policy required)
- ✅ Terms of Service template
- ✅ Privacy Policy template
- ✅ Cookie consent management

## Success Criteria

**Before launch to production:**

✅ All authentication flows working
✅ Users can CRUD listings without errors
✅ Bulk import handles 10,000+ rows
✅ AI description generation <2s
✅ Analytics dashboard displays real data
✅ Lead capture working end-to-end
✅ 95%+ test coverage on critical paths
✅ <1s page load time on all pages
✅ No console errors
✅ Mobile responsive design
✅ Accessibility (WCAG AA minimum)
✅ Zero hardcoded secrets
✅ Comprehensive API documentation
✅ User onboarding is frictionless (<5 min to first value)

## Post-MVP Roadmap

See `03_ROADMAP.md` for Phase 2 and Phase 3 features.
