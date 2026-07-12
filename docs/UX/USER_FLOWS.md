# DirectoryOS User Flows

## Overview

This document describes the primary end-to-end user flows for DirectoryOS. Each flow is described step-by-step at the interaction/process level, based on the approved product and architecture documentation (`docs/Product/01_PROJECT.md`, `docs/Product/02_MVP.md`, `docs/Product/03_ROADMAP.md`, `docs/Architecture/ARCHITECTURE.md`, `docs/Architecture/API.md`, `docs/Architecture/DATABASE.md`).

This document is UX/process documentation only. It does not contain UI code, component implementations, or visual design specifications.

## Table of Contents

1. [Onboarding Flow](#1-onboarding-flow)
2. [Authentication Flow](#2-authentication-flow)
3. [Listing Management Flow](#3-listing-management-flow)
4. [AI Workflow](#4-ai-workflow)
5. [Lead Workflow](#5-lead-workflow)
6. [Billing Workflow](#6-billing-workflow)
7. [Admin Workflow](#7-admin-workflow)

---

## 1. Onboarding Flow

**Goal**: Get a new organization from signup to first value (a published listing or imported data) as quickly as possible (target: <5 minutes to first value).

**Actors**: New user (future organization Admin)

**Steps**:

1. User navigates to the signup page and submits email + password (or completes Google OAuth, future phase).
2. System creates the `users` record and sends a verification email via Resend.
3. User clicks the verification link; email is marked `email_verified`.
4. User is prompted to create their organization:
   - Organization name
   - Timezone
   - Industry/category
   - Subscription tier selection (Starter, Professional, Enterprise) with a 14-day free trial applied by default
5. System creates the `organizations` record and links the user to it as `admin`.
6. User is presented with a choice of starting path:
   - **Path A: Create first listing** — user is guided through the listing creation form (see Listing Management Flow).
   - **Path B: Bulk import** — user is guided into the CSV import flow (see Listing Management Flow → Import Workflow).
7. User is taken to the main dashboard, which displays a getting-started checklist and a sample/template view of analytics (populated once real data exists).
8. System tracks onboarding completion milestones (organization created, first listing created/imported, first AI generation used, first lead received) to measure time-to-value.
9. Trial status and remaining days are surfaced in the dashboard header until the user selects a paid plan or the trial ends (see Billing Workflow).

**Exit criteria**: Organization exists, at least one team member is authenticated, and the user has either created or imported at least one listing.

---

## 2. Authentication Flow

**Goal**: Provide secure, frictionless access control per the RBAC model (Admin, Editor, Viewer) with JWT-based sessions.

**Actors**: Any user (new or returning)

### 2.1 Sign Up

1. User submits email and password on the signup form.
2. System validates input (email format, password strength) via server-side validation (Zod schemas).
3. System creates the user record with a hashed password (bcrypt via Supabase Auth) and `email_verified = false`.
4. Verification email is sent via Resend.
5. User confirms email via the link; account becomes fully active.

### 2.2 Login

1. User submits email and password (or OAuth provider, future phase).
2. Supabase Auth verifies credentials.
3. On success, system issues a short-lived access token (JWT, 15-minute expiry) and a long-lived refresh token (30-day expiry, httpOnly cookie).
4. User is redirected to the dashboard scoped to their organization (enforced via Row-Level Security).
5. On failure, system returns an error and does not reveal whether the email or password was incorrect.

### 2.3 Session Refresh

1. Client detects an expired or near-expired access token.
2. Client silently calls the refresh endpoint with the refresh token cookie.
3. System validates the refresh token and issues a new access token.
4. If the refresh token is invalid or expired, user is signed out and redirected to login.

### 2.4 Password Reset

1. User selects "Forgot password" and submits their email.
2. System sends a password-reset link via Resend (regardless of whether the email exists, to avoid account enumeration).
3. User opens the link, sets a new password.
4. System invalidates existing sessions/refresh tokens for that user and requires re-login.

### 2.5 Logout

1. User selects "Log out."
2. System invalidates the refresh token and clears session cookies.
3. User is redirected to the login page.

### 2.6 Role Enforcement

1. On every authenticated request, the system verifies the JWT and resolves the user's `organization_id` and `role`.
2. Row-Level Security policies restrict data access to the user's organization.
3. Role-based checks gate specific actions:
   - **Admin**: full access, including billing, team management, and organization settings.
   - **Editor**: manage listings, leads, and view analytics; no billing or team management access.
   - **Viewer**: read-only access to listings and analytics.
4. Unauthorized actions return a 403 response and the UI surfaces an access-denied state.

---

## 3. Listing Management Flow

**Goal**: Allow organizations to create, edit, organize, and publish business listings, individually or in bulk.

### 3.1 Browse & Manage Listings

1. User opens the Listings dashboard, which loads a paginated table of the organization's listings.
2. User searches or filters by category, status (draft/published/archived), or name.
3. User sorts results by date, name, or lead count.
4. User selects one or more listings to perform a bulk action (e.g., archive, change category, delete) or opens a single listing to edit.

### 3.2 Create a Listing

1. User selects "New Listing."
2. User fills in required fields: business name, category (multi-select), description, phone, email, website, address (with Mapbox autocomplete), hours of operation, images, and social links.
3. System auto-saves the draft periodically to prevent data loss.
4. User optionally invokes AI-assisted description generation (see AI Workflow).
5. User previews the listing as it will appear publicly.
6. User reviews the SEO/content quality score and addresses any flagged issues.
7. User publishes the listing; status changes from `draft` to `published`.
8. System records the change in the listings audit trail.

### 3.3 Edit a Listing

1. User opens an existing listing from the dashboard (inline edit or full form).
2. User updates one or more fields.
3. System validates changes and auto-saves drafts.
4. User reviews updated SEO score if content changed.
5. User publishes/saves changes; audit trail is updated with the change, timestamp, and acting user.

### 3.4 Bulk Import Workflow

1. User selects "Bulk Import" from the dashboard.
2. User uploads a CSV file.
3. System runs a mapping wizard, auto-detecting column-to-field matches where possible; user confirms or adjusts mappings.
4. System validates the data and flags duplicates against existing listings.
5. User previews the parsed data before confirming.
6. User confirms the import; system processes rows in the background with real-time progress tracking.
7. System reports results, including a detailed error log for any failed rows.
8. User may roll back a failed or unwanted import.

### 3.5 Bulk Export Workflow

1. User selects "Export" from the dashboard, optionally applying the current filter/search as scope.
2. System generates a CSV/Excel-compatible export of the selected listings.
3. System logs the export action (user, timestamp) in the audit trail.
4. User downloads the file.

---

## 4. AI Workflow

**Goal**: Use AI (Claude/Gemini) to accelerate content creation and quality improvement, always with human review before publishing.

### 4.1 AI Description Generation (Single Listing)

1. From the listing edit form, user selects "Generate with AI."
2. System builds a prompt using the listing's category, existing description (if any), industry best practices, and target SEO keywords.
3. System calls the AI provider and returns a generated description (target: <2 seconds).
4. Generated content is shown to the user as a suggestion, not auto-applied.
5. User reviews, edits if needed, and accepts or rejects the suggestion.
6. If accepted, system stores the new description and retains the previous version in description history for rollback.

### 4.2 Bulk AI Generation

1. User selects multiple listings from the dashboard and chooses "Bulk Generate Descriptions."
2. System queues generation requests with rate limiting to respect AI provider limits.
3. User monitors progress as each listing's description is generated.
4. Each generated description enters the same human-review step before publishing; nothing is auto-published.

### 4.3 SEO Analysis

1. User requests an SEO analysis for a listing (individually or as part of a batch audit).
2. System evaluates title optimization, meta description, keyword density, and readability (Flesch-Kincaid).
3. System returns actionable suggestions rather than a bare score.
4. User applies suggested changes manually or regenerates content via AI, then re-checks the score.

### 4.4 Content Quality Scoring

1. On save or publish, system automatically evaluates completeness (required fields filled), image quality, description length, and category appropriateness.
2. System surfaces a quality score and specific gaps.
3. User addresses gaps before publishing, or publishes with an acknowledged lower score if permitted by their role.

---

## 5. Lead Workflow

**Goal**: Capture, qualify, and manage leads generated from published listings.

### 5.1 Lead Capture (End Visitor)

1. A visitor views a published listing and submits the embedded contact form (name, email, phone, message, optional custom fields).
2. Form submission is validated and passed through spam protection (reCAPTCHA).
3. If double opt-in is enabled, visitor confirms their email before the lead is finalized.
4. System creates a `leads` record linked to the listing, with `source = form` and `status = new`.
5. System calculates an automated lead quality score.

### 5.2 Lead Notification

1. On lead creation, system triggers a notification event (`lead.captured`).
2. Organization users receive an email alert (via Resend) and an in-app notification.
3. Users may also opt into a daily digest email summarizing new leads.

### 5.3 Lead Management (Organization User)

1. User opens the Leads dashboard, viewing all captured leads for the organization.
2. User filters/searches leads by status, listing, or quality score.
3. User updates lead status as it progresses: `new` → `contacted` → `converted`/`lost`.
4. Status changes trigger a `lead.status_changed` event and are reflected in reporting.
5. User can perform bulk actions (mark as contacted, delete) across selected leads.
6. User exports leads to CSV for use in external tools (Salesforce integration planned for a future phase).

### 5.4 Lead Analytics

1. User views per-listing lead metrics: view count, click-through rate, lead capture count, time-to-first-lead, device breakdown, and traffic source.
2. User reviews aggregate lead trends on the main dashboard (this month vs. last month).

---

## 6. Billing Workflow

**Goal**: Manage subscription plans and payment processing through Stripe, tied to organization-level access.

**Actors**: Organization Admin (billing actions are restricted to the Admin role)

### 6.1 Trial to Paid Conversion

1. Organization starts on a 14-day free trial upon signup, with a selected subscription tier (Starter, Professional, or Enterprise).
2. Dashboard displays trial status and days remaining throughout the trial period.
3. Admin selects "Upgrade" or is prompted automatically as the trial nears expiration.
4. Admin is directed to a Stripe-hosted checkout/billing portal to enter payment details and confirm the plan.
5. Stripe processes the payment and returns a success/failure result.
6. On success, system updates `organizations.subscription_tier` and `subscription_status` to `active`; system emits a `subscription.created` event.
7. On failure, user remains on the trial/free state and is shown the error returned by Stripe.

### 6.2 Plan Changes

1. Admin opens billing settings and selects a different tier (upgrade or downgrade).
2. System redirects to the Stripe billing portal to confirm the change and prorate charges as applicable.
3. On confirmation, system updates the organization's `subscription_tier` accordingly.
4. Listing limits and feature access (per tier: Starter, Professional, Enterprise) are re-evaluated immediately based on the new tier.

### 6.3 Payment Failure / Dunning

1. Stripe attempts a recurring charge and it fails.
2. Stripe emits a webhook event to DirectoryOS.
3. System updates `subscription_status` (e.g., `past_due`) and notifies the Admin via email and in-app banner.
4. Admin updates payment details via the Stripe billing portal.
5. Once payment succeeds, `subscription_status` returns to `active`.

### 6.4 Cancellation

1. Admin selects "Cancel subscription" from billing settings.
2. Admin confirms cancellation (with any applicable retention messaging).
3. System updates `subscription_status` to reflect cancellation (e.g., cancels at period end) and emits a `subscription.cancelled` event.
4. Organization retains access until the end of the current billing period, after which access is downgraded or restricted per policy.

---

## 7. Admin Workflow

**Goal**: Allow organization Admins to manage team members, organization settings, and oversee sensitive operations; also covers platform-level oversight.

### 7.1 Organization Settings

1. Admin opens Organization Settings.
2. Admin updates organization-level details: name, timezone, industry, and branding/preferences.
3. Changes are saved and reflected across the organization (all team members see updated settings).

### 7.2 Team Member Management

1. Admin opens the Team/Members section.
2. Admin invites a new team member by email, assigning a role (Admin, Editor, or Viewer).
3. Invitee receives an email invitation (via Resend) and completes signup/login to join the organization.
4. Admin can change an existing member's role or remove a member from the organization.
5. Role changes take effect immediately and are enforced via Row-Level Security and role checks on subsequent requests.
6. All membership and role changes are recorded in the audit trail.

### 7.3 Audit & Compliance Oversight

1. Admin opens the audit/activity log to review sensitive actions: listing changes, imports/exports, role changes, and billing events, each with timestamp and acting user.
2. Admin can filter the log by user, listing, or action type to investigate specific changes.
3. Admin can initiate data export or deletion requests to support GDPR/CCPA compliance obligations.

### 7.4 Platform-Level Administration (Internal Operations)

1. Internal platform operators monitor system health via observability tooling (Sentry for errors, Vercel Analytics for performance, Supabase logs).
2. Operators review security events and access logs as part of regular monitoring (weekly dashboard review, monthly security audit).
3. Operators respond to incidents following the disaster recovery plan (failover, point-in-time recovery) when needed.
4. Operators track cost and usage across Vercel, Supabase, and third-party integrations as part of monthly operational review.
