# DirectoryOS Database Review

**Prepared by**: Database/Architecture Review
**Purpose**: Evaluate `DATABASE.md` against the Product documentation (`01_PROJECT.md`, `02_MVP.md`, `03_ROADMAP.md`, `04_IDEAS.md`, `PRODUCT_REVIEW.md`) and `ARCHITECTURE.md`/`API.md` to surface gaps before further schema work.
**Scope**: `docs/Architecture/DATABASE.md`
**Non-goals**: This review does not rewrite the schema, remove tables/columns, or propose a migration. All recommendations are additive and intended to inform future migrations.

---

## 1. Normalization

**Current state**: The schema is mostly in 3NF for core entities (`organizations`, `users`, `listings`, `leads`, `analytics_events`). However, several JSONB columns store what is effectively structured, queryable data:

- `listings.categories` (JSONB array) duplicates the single `listings.category` column — two competing representations of the same concept.
- `listings.hours`, `listings.social_links`, `listings.images`, `listings.seo_keywords` are JSONB blobs rather than normalized child tables, which is reasonable for flexible/sparse data but makes filtering, validation, and reporting (e.g., "listings open on Sundays") harder.
- `organizations.settings` and `users.settings` are unstructured JSONB with no documented schema, which risks becoming a dumping ground for fields that should be first-class columns (e.g., billing-related settings — see Section 9).

**Recommendation**: Clarify in `DATABASE.md` which fields are intentionally denormalized (JSONB for flexibility/speed) versus candidates for future normalization (e.g., a `listing_categories` join table once multi-category filtering/reporting becomes a requirement per `02_MVP.md`'s "Category (multi-select)"). Document the decision criteria (query patterns vs. flexibility) rather than defaulting to JSONB everywhere.

---

## 2. Entity Relationships

**Current state**: Core relationships (`organizations` → `users`, `organizations`/`listings` → `leads`, `listings` → `listings_audit`) are documented with explicit foreign keys.

**Gaps relative to Product docs**:
- **Directories vs. Organizations**: `PRODUCT_REVIEW.md` (Section: Organizations) flags that DirectoryOS is described as managing "directories," but the schema only models a single `organizations` entity with no explicit `directories` concept. If an organization can operate multiple directories (per `01_PROJECT.md`'s "Niche Directory Creators" and white-label roadmap items), the current 1:1 assumption between organization and directory is undocumented and unvalidated.
- **Categories relationship**: `categories` is a real table with `parent_id` self-reference, but `listings.category`/`listings.categories` do not reference `categories.id` — they are free-text/JSONB values. This means the categories taxonomy and the listing's actual category assignment are disconnected.
- **API keys ownership**: `api_keys.created_by` references `users.id` but has no `ON DELETE` behavior specified, unlike other FKs which explicitly set `CASCADE` or `SET NULL`.

**Recommendation**: Document the intended relationship between organizations and (future) directories explicitly, even if the current MVP collapses them 1:1, so the assumption is visible to future contributors. Consider documenting whether `listings.category`/`categories` should reference `categories.id` (or note this as a known interim gap).

---

## 3. Tenant Isolation

**Current state**: `organization_id` is present as a NOT NULL foreign key on `users`, `listings`, `leads`, `analytics_events`, `listings_audit`, and `api_keys`, and RLS policies are documented for tenant-scoped SELECT/UPDATE/DELETE on `listings`.

**Gaps**:
- RLS policy examples are only shown for `listings`. `DATABASE.md` enables RLS on all 8 tables but only documents policies for one — there's no example (or note) for `users`, `leads`, `analytics_events`, `listings_audit`, `categories`, or `api_keys`, leaving tenant-isolation enforcement for those tables unverified in documentation.
- `categories.organization_id` is nullable (`REFERENCES organizations(id)`, not `NOT NULL`), implying support for global/shared categories alongside org-specific ones, but this dual-purpose design isn't explained — a reader can't tell if NULL is intentional (shared taxonomy) or an oversight.
- No mention of tenant isolation testing (e.g., automated tests verifying cross-tenant queries are blocked), which `02_MVP.md`'s security requirements imply are needed ("Audit logging of sensitive actions") but don't explicitly cover RLS verification.

**Recommendation**: Add RLS policy examples (or at least a statement of the policy pattern) for every RLS-enabled table, and document the intentional NULL semantics of `categories.organization_id`. Reference tenant-isolation test coverage as part of the security requirements already listed in `02_MVP.md`.

---

## 4. Foreign Keys

**Current state**: Foreign keys are used consistently for tenant-scoping (`organization_id`) and core relationships, with explicit `ON DELETE` behavior in most cases (`CASCADE` for tenant-owned data, `SET NULL` for optional references like `analytics_events.user_id`).

**Gaps**:
- `api_keys.created_by UUID REFERENCES users(id)` has no `ON DELETE` clause — default Postgres behavior is `NO ACTION`, meaning deleting a user could be blocked or leave ambiguous state if not handled at the application layer. This is inconsistent with the `SET NULL` pattern used elsewhere for "who did this" references (e.g., `listings_audit.user_id`, `analytics_events.user_id`).
- `categories.parent_id UUID REFERENCES categories(id)` also has no `ON DELETE` behavior specified, which matters once categories can be deleted/soft-deleted (see Section 8) — orphaned child categories are a risk.
- No documented composite/covering foreign key strategy for the audit or analytics tables beyond individual columns (e.g., no note on whether `(organization_id, listing_id)` consistency is enforced at the DB level or only application level).

**Recommendation**: Add explicit `ON DELETE` behavior for `api_keys.created_by` and `categories.parent_id` (likely `SET NULL` for both, consistent with existing patterns), and note whether cross-column consistency (e.g., a lead's `listing_id` and `organization_id` always matching) is enforced via constraint/trigger or left to application logic.

---

## 5. Indexing

**Current state**: Indexing is reasonably thorough — foreign keys, status/filter columns, slugs, and a geospatial GIST index are all covered, and the "Performance Tuning" section documents the indexing philosophy.

**Gaps relative to Product performance targets** (`02_MVP.md` Performance Targets table):
- `02_MVP.md` requires "Search 1,000 listings <500ms" with search/filtering "by category, status, name" — but there is no full-text search index (e.g., `GIN` on a `tsvector` for `name`/`description`) documented, only a plain B-tree-style index implied for `name`/`slug`. Substring or fuzzy name search would not benefit from a standard index.
- No index is documented for `leads(email)` or `leads(phone)`, which would matter for duplicate-lead detection mentioned in bulk-import ("Duplicate detection" in `02_MVP.md`).
- No composite index for common dashboard queries like `listings(organization_id, status, created_at)` (list view with filter + sort), which is the primary MVP dashboard use case ("Table view... Search & filtering... Sorting... Pagination").
- `analytics_events` has good time-based indexes, but at "100,000+ listings" and high event volume (per `01_PROJECT.md` scalability goals), this table will grow fastest and has no documented partitioning strategy tied to indexing (see Section 6).

**Recommendation**: Add a full-text search index for `listings.name`/`description` (e.g., `GIN` + `tsvector`), a composite index for the common dashboard filter/sort pattern, and consider indexes on `leads.email`/`leads.phone` to support duplicate detection.

---

## 6. Scalability

**Current state**: `DATABASE.md` states "Scaling: Vertical (Supabase tiers)" and documents connection pooling limits (100/200+ connections).

**Gaps relative to Product docs**:
- `01_PROJECT.md` explicitly states "Scalability: Support 100,000+ listings without performance degradation" and "Architecture supports millions of listings" as a Key Principle, but `DATABASE.md`'s scaling strategy is vertical-only with no mention of read replicas, partitioning, or horizontal scaling paths — a direct tension with the stated product goal.
- `analytics_events`, the highest-growth table (one row per view/click/lead/share/call across all listings), has no documented partitioning strategy (e.g., time-based partitioning by month), despite a 1-year retention policy already being documented in "Data Retention." Retention without partitioning means deletes will be expensive table-wide operations.
- No caching layer is mentioned (e.g., Redis/Supabase cache) for frequently-read, rarely-changed data like `categories` or published `listings`, despite `01_PROJECT.md`'s "Sub-100ms page loads" principle.
- No documented approach to the "100,000+ listings" target beyond indexing — e.g., no note on read/write ratio expectations or query load testing.

**Recommendation**: Add a note reconciling the "vertical only" scaling statement with the product's stated 100,000+/millions-of-listings goals — even if the near-term (MVP) plan is vertical scaling with a documented upgrade path (read replicas, partitioning `analytics_events`, caching) for Phase 2/3 scale, this should be explicit rather than implied.

---

## 7. Audit Logging

**Current state**: `listings_audit` captures `action`, `changes` (JSONB), `previous_values`, `user_id`, and timestamps, with `02_MVP.md` explicitly listing "Audit logging of sensitive actions" as a security requirement.

**Gaps**:
- Audit logging is scoped only to `listings` (`listings_audit`). Other sensitive actions implied by the product docs are not covered:
  - **User/role changes** (e.g., promoting a viewer to admin, deactivating a user) — no `users_audit` or equivalent.
  - **Organization-level changes** (e.g., subscription tier changes, settings changes) — no audit trail for `organizations`.
  - **Lead status changes** (`leads.status` transitions like "contacted" → "converted") mentioned in `02_MVP.md`'s Lead Management, but no audit table for leads.
  - **API key usage/creation/revocation** — `api_keys` has `last_used_at` but no audit trail of key creation, permission changes, or revocation events.
- "Audit logs retained for 1 year" is stated in Data Retention, but there's no documented mechanism (scheduled job, trigger, retention policy enforcement) for actually purging/archiving audit data after 1 year.
- GDPR/CCPA compliance is claimed in `02_MVP.md` ("data export, deletion"), but audit data referencing a deleted (GDPR-erased) user's actions isn't addressed — e.g., what happens to `listings_audit.user_id` if a user requests full erasure, versus a soft delete.

**Recommendation**: Note the current audit scope is listings-only and flag that user/organization/lead/API-key audit trails are a documented gap, to be planned before those actions are considered "audited" per the MVP security requirement. Add a note on how audit retention (1 year) will actually be enforced (e.g., scheduled deletion job) and how it interacts with GDPR erasure requests.

---

## 8. Feature Flags

**Current state**: `DATABASE.md` has no feature flag table or column, and no mention of feature flags at all.

**Product context**: `ARCHITECTURE.md` mentions "Feature flags for experimental APIs" and `03_ROADMAP.md`'s Phase 2 plan explicitly calls for "Feature flags for safe rollout." Additionally, subscription tiers (`starter`/`professional`/`enterprise`) imply feature-gating by plan (`PRODUCT_REVIEW.md` explicitly flags "no pricing, feature-gating matrix" as a gap).

**Gap**: There is no data model for either (a) operational feature flags (rollout/experimentation) or (b) plan-based feature entitlements (which features/limits apply to which subscription tier). Currently `max_listings`/`max_team_members` are hardcoded numeric columns on `organizations`, which only covers two specific limits, not a general entitlements/feature-gating model.

**Recommendation**: Document that feature flagging is currently out of scope for the schema and flag it as needed before Phase 2 (per `03_ROADMAP.md`'s own timeline). If a lightweight table (e.g., `feature_flags` + `organization_feature_overrides`, or a `plan_features` entitlement table referenced by `subscription_tier`) is anticipated, note it here as a forward-looking placeholder without committing to a design yet.

---

## 9. Soft Deletes

**Current state**: `deleted_at` columns exist on `organizations`, `users`, `listings`, and `leads`, with partial unique indexes (`WHERE deleted_at IS NULL`) correctly used for `users` and `listings` to allow re-use of emails/slugs after soft deletion.

**Gaps**:
- `categories` and `api_keys` do **not** have `deleted_at` columns — categories are hard-deleted (via `ON DELETE CASCADE` from organizations, or presumably direct deletes), which conflicts with the `parent_id` self-reference risk noted in Section 4, and `api_keys` relies on `is_active` (a boolean flag) rather than `deleted_at`, which is an inconsistent soft-delete pattern within the same schema.
- `analytics_events` and `listings_audit` have no `deleted_at` — this is likely intentional (append-only/immutable logs), but it isn't stated, so a reader can't distinguish "intentionally immutable" from "missed adding soft delete."
- No documented cascade behavior for soft deletes — e.g., when a `listing` is soft-deleted, are its `leads` also considered soft-deleted, or do they remain independently active? This matters for the "leads" list showing leads tied to a now-deleted listing.
- GDPR "right to erasure" (`02_MVP.md`) requires actual hard deletion at some point, but there's no documented process for converting a soft-deleted record into a hard-deleted/anonymized one after a retention period.

**Recommendation**: Add `deleted_at` to `categories` for consistency, or explicitly document why it's excluded. Clarify whether `analytics_events`/`listings_audit` are intentionally exempt from soft-delete (append-only logs). Document the soft-delete cascade relationship between listings and their leads, and describe (even briefly) how soft-deleted data eventually satisfies GDPR erasure requirements.

---

## 10. Billing Support

**Current state**: `organizations` has `subscription_tier`, `subscription_status`, `max_listings`, and `max_team_members` columns only. No dedicated billing tables exist.

**Product context**: This is the most significant gap identified. `01_PROJECT.md` lists Stripe as a core integration ("Payment Processing - Stripe integration for subscriptions and marketplace features") and `ARCHITECTURE.md` describes Stripe handling "Subscription management, Payment processing, Billing portal." `API.md` references `subscription.created`/`subscription.cancelled` webhook events. `PRODUCT_REVIEW.md` independently flags this exact gap: *"no billing/subscription/invoice data model exists yet despite Stripe being a listed integration"* and calls out missing usage tracking, billing lifecycle (proration, dunning, invoicing), and AI cost/usage metering per organization.

**Specific gaps**:
- No `subscriptions` or `invoices` table to store Stripe customer ID, subscription ID, billing period, price/plan ID, or payment status — `subscription_status` is a bare string with no documented enum values or link to a Stripe object.
- No `usage_records`/metering table for tracking consumption against tier limits (listings created, AI generations used, API calls) despite `max_listings`/`max_team_members` implying limits need to be checked somewhere.
- No table for tracking failed payments, dunning state, or trial period (`02_MVP.md`'s onboarding flow explicitly mentions "Claim free trial (14 days)" but there's no `trial_ends_at` or equivalent column).
- No webhook event log/idempotency table for handling Stripe webhooks reliably (a common requirement to avoid duplicate processing).

**Recommendation**: This is the highest-priority gap to address before billing work begins. At minimum, document (without designing in full) that a `subscriptions` table (Stripe customer/subscription IDs, plan, period, trial end), a `usage_records`/metering mechanism, and a webhook idempotency log are anticipated additions, consistent with `PRODUCT_REVIEW.md`'s own recommendation to capture this "before schema finalization."

---

## Summary Table

| Area | Status | Priority |
|---|---|---|
| Normalization | ⚠️ Mostly sound; JSONB usage under-documented | Low |
| Entity Relationships | ⚠️ Organization↔Directory and category linkage undocumented | Medium |
| Tenant Isolation | ⚠️ RLS only exemplified for one table | Medium |
| Foreign Keys | ⚠️ Missing `ON DELETE` on two FKs | Low |
| Indexing | ⚠️ No full-text search or common dashboard composite index | Medium |
| Scalability | ⚠️ Vertical-only strategy conflicts with stated product goals | High |
| Audit Logging | ⚠️ Scoped to listings only; no retention enforcement documented | Medium |
| Feature Flags | ❌ Not modeled at all | Medium |
| Soft Deletes | ⚠️ Inconsistent across tables; no cascade/erasure documentation | Medium |
| Billing Support | ❌ No billing data model despite Stripe being committed | **High** |

---

## Overall Recommendation

`DATABASE.md` is a solid MVP-stage schema with good tenant-scoping fundamentals (organization_id everywhere, RLS enabled, soft deletes on primary entities, audit trail for listings). The most consequential gaps — **billing support** and **scalability strategy** — directly mirror gaps already identified in `PRODUCT_REVIEW.md` and should be resolved through documentation additions (not a schema rewrite) before Phase 2 work begins, per the existing `03_ROADMAP.md` timeline. Feature flags, expanded audit logging, and indexing improvements are lower-urgency but should be tracked so they aren't forgotten once Phase 2/3 features (white-label, marketplace, feature-flagged rollouts) begin.
